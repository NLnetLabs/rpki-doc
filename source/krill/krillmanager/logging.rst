.. _doc_krill_manager_logging:

Logging
=======

In Krill Manager when we refer to logs we primarily refer to a series of
(mainly) unstructured messages, not to metrics such as counters and guages
exposed by Prometheus endpoints.

On a Krill Manager host `journald <https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html>`_
is the primary log subystem and Docker container logs are routed to the journal
via the `Docker journald logging driver <https://docs.docker.com/config/containers/logging/journald/>`_.

-----------
Log Viewing
-----------

- Host logs can be viewed in the usual way with ``journalctl`` and via files
  stored in ``/var/log/``.
- Primary Krill Manager logs can be viewed with the
  :ref:`krillmanager logs <cmd_logs>` command.
- Other Krill Manager logs can be viewed with the ``docker service logs``
  command.

.. tip:: In cluster mode :ref:`krillmanager logs <cmd_logs>` and
         ``docker service logs`` can be used to view logs even if the source
         container is on a slave cluster node.

----------------------------------
Log Aggregation, Upload & Analysis
----------------------------------

Using `FluentD <https://www.fluentd.org/>`_ Krill Manager can:
  - aggregate journal logs across all cluster nodes together.
  - stream journal logs to an AWS S3 compatible storage service.
  - stream journal logs to one of `many 3rd party services <https://www.fluentd.org/dataoutputs>`_
    for external processing and analysis.


Using `s3cmd <https://s3tools.org/s3cmd>`_ Krill Manager can:
  - upload Krill RFC audit log files to an AWS S3 compatible storage service.

.. note:: FluentD and s3cmd related Krill Manager Docker services are only
          created if log uploading was enabled during :ref:`doc_krill_manager_initial_setup`.

Upload Frequency
----------------

RFC protocol exchange logs are uploaded hourly. All other logs are uploaded at
least every 10 minutes, more frequently if there is a lot of logging activity.

Force Flush
-----------

If needed you can force FluentD to flush its buffers which should cause it to
stream any data it has pending to the destination, e.g. S3 compatible storage or
a custom destination that you have configured:

1. Use ``docker service ps krill_log_uploader`` to find the server running the
   log upload container.
2. SSH to the server running the log upload container.
3. Use ``docker ps`` to find the the container ID or name of the
   ``krill_log_uploader`` container.
4. Use ``docker kill -s USR1 <container PID/name>`` to send the flush signal to
   FluentD.
5. Use ``docker logs <container PID/name>`` to see that the flush was received and
   if it caused any upload activity, e.g.:
.. code-block:: bash

   # docker service logs --raw z1c6ksk6zvdx | fgrep flush
   2020-04-21 08:44:25 +0000 [info]: #0 force flushing buffered events
   2020-04-21 08:44:25 +0000 [info]: #0 flushing all buffer forcedly

Log Retention
-------------

When log upload is enabled, local copies of Krill RFC audit logs are deleted
after two days as these logs can become quite large. All other logs are
rotated according to the default journald behaviour and logrotate
configuration.

Log Bucket Structure
--------------------

When using the default ``s3.conf`` fluentd config file, uploaded logs are
structured like so:

.. code-block:: bash
 
   /<Bucket Directory>/rfc_trail
   /<Bucket Directory>/YYYY/MM/DD/HH/<hostname>/<service>.<N>.gz
   /<Bucket Directory>/YYYY/MM/DD/HH/<hostname>/<container>/<instance id>.<N>.gz

Where ``<Bucket Directory>`` is the value you provided to the wizard.

Log File format
---------------

The format of the files is dependent on the type of log file:

- ``rfc_trail`` log files are in a Krill internal binary format.
- ``<service>`` log files are in JSON format.
- ``<container>`` log file are in JSON format with additional fields.

This SSHD log message shows a ``<service>`` log line example:

.. code-block:: json

   {
     "hostname": "demomaster",
     "source": "syslog",
     "syslog_id": "sshd",
     "ts_epoch_ms": "1586277165425045",
     "message": "Invalid user test from 104.236.250.88 port 49112"
   }

This NGINX access log message shows a ``<container>`` log line example:

.. code-block:: json

   {
     "hostname": "demomaster",
     "source": "journal",
     "syslog_id": "6ef2bbf3eba9",
     "ts_epoch_ms": "1586278786997270",
     "container": "krill_nginx.w2ia8pd3b2kxqm77uwyepooqh.o3lv5trgdnykegaeo9ylhs9d5",
     "message": "::ffff:104.206.128.2 - - [07/Apr/2020:16:59:46 +0000] \"GET / HTTP/1.1\" 404 153 \"-\" \"https://gdnplus.com:Gather Analyze Provide.\" \"-\"",
     "image": "krillmanager/http-server:v0.1.0@sha256:f88c52b73abf86c3223dcf4c0cc3ff8351f61e74ee307aa8c420c9e0856678f7"
   }

----------------
Custom Behaviour
----------------

.. Warning:: When providing custom configuration files you should use the
             ``krillmanager edit`` command to create and edit configuration
             files so that the changes are properly replicated across all
             cluster nodes.

Customising Log Streaming
-------------------------

Files in ``/fluentd-conf/*.conf`` can be edited with ``krillmanager edit`` to configure fluentd according to
your own design, streaming logs to any of the many 3rd party services that
fluentd supports.

To force fluentd to reload the configuration either restart all services with
``krillmanager restart`` or only the fluentd processor service with
``docker service restart krill_log_uploader --force``.

To force fluentd to flush its buffers you can use the
``docker kill -s SIGUSR1 <container name/id>`` command on the node where the
``krill_log_uploader`` container is running.

.. seealso::
     - `fluentd: List of Data Outputs <fluentd.org/dataoutputs>`_
     - `fluentd: Input / Output Plugins <https://www.fluentd.org/plugins/all#input-output>`_

Customising Audit Log Upload
----------------------------

The ``/s3cmd-conf/s3cmd.conf`` file can be edited with ``krillmanager edit`` to take advantage of any additional
features of your S3-like service provider that s3cmd supports.

.. seealso::
     - `About the s3cmd configuration file <https://s3tools.org/kb/item14.htm>`_

------------
Log Analysis
------------

.. Tip:: Upload to an AWS S3 compatible service is primarily intended for
         archival and root cause analysis after an incident. If your intention
         is to extract interesting metrics or you would like a more visual way
         to interact with your logs we suggest feeding tools like Grafana Loki
         or Elastic Search from FluentD.

Some basic analysis can be done on the uploaded logs by using ``s3cmd`` to fetch
logs of interest, ``jq`` to parse and extract data from the JSON log structure,
and standard Linux command line tools to further extract and report on the data.

For example, assuming that you have configured Krill Manager to store logs in a
DigitalOcean Space, you can generate a report of RRDP clients visiting your
Krill Manager instance on a particular date like so:

.. code-block:: bash

   $ DATE_OF_INTEREST="2020/05/11"
   $ S3_BUCKET_NAME="my-bucket-name"
   $ export AWS_ACCESS_KEY_ID="your-access-key"
   $ export AWS_SECRET_ACCESS_KEY="your-secret-access-key"
   $ docker run -it --rm \
      -v /tmp/logs:/mnt/logs \
      -e AWS_ACCESS_KEY_ID \
      -e AWS_SECRET_ACCESS_KEY \
      --entrypoint=s3cmd \
      krillmanager/log-uploader:v0.1.1 \
        get \
          -r \
          --host-bucket="%(bucket)s.ams3.digitaloceanspaces.com" \
          --rexclude=".*" \
          --rinclude=".*${DATE_OF_INTEREST}.*/krill_nginx/.*" \
          s3://${S3_BUCKET_NAME}/logs/ /mnt/logs/
   $ find /tmp/logs/ \
       -name '*.gz' \
       -exec zcat {} \; | \
         jq -r '.message | select(contains("/rrdp/"))' | \
           grep -oP '[0-9]+ [0-9]+ "-" \K"[^"]+"' | \
             cut -d '"' -f 2 | \
               sort | \
                 uniq -c | \
                   sort -rn

This will produce a breakdown of RRDP clients by their HTTP User Agent and the
number of times that agent was seen requesting a URL containing ``/rrdp/``,
highest count first.