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

Files in ``/fluentd-conf/*.conf`` can be edited with ``krillmanager edit`` to
configure fluentd according to your own design, streaming logs to any of the
many 3rd party services that fluentd supports. Configuration elemnents should be
placed inside a label stanza like so:

.. parsed-literal::

   <label @ready>
     <match \*\*>
       @type s3
       ..
     </match>
   </label>

When working with Fluentd configuration files note the following useful
commands:


.. code-block:: bash

    # Reload the Fluentd configuration:
    docker service restart krill_log_uploader --force

    # Flush Fluentd output buffers:
    docker kill -s SIGUSR1 <krill_log_uploader container name/id>

.. seealso::
     - `fluentd: List of Data Outputs <fluentd.org/dataoutputs>`_
     - `fluentd: Input / Output Plugins <https://www.fluentd.org/plugins/all#input-output>`_

Diagnosing Streaming Problems
-----------------------------

Krill Manager v0.2.2 added a Fluentd Prometheus metrics endpoint on port 24231
at ``/metrics``. The statistics published at this endpoint can help identify
whether events are being received and handled by the expected Fluentd output
plugins.

Customising Audit Log Upload
----------------------------

The ``/s3cmd-conf/s3cmd.conf`` file can be edited with ``krillmanager edit`` to take advantage of any additional
features of your S3-like service provider that s3cmd supports.

.. seealso::
     - `About the s3cmd configuration file <https://s3tools.org/kb/item14.htm>`_

-----------------
Analysis Examples
-----------------

Manual Log Analysis
-------------------

.. Tip:: Upload to an AWS S3 compatible service is primarily intended for
         archival and root cause analysis after an incident. If your intention
         is to extract interesting metrics or you would like a more visual way
         to interact with your logs we suggest feeding tools like Grafana Loki
         or Elastic Search from FluentD.

Assuming that you have configured Krill Manager to store logs in a DigitalOcean
Space, you can generate a report of RRDP clients visiting your Krill Manager
instance on a particular date like so:

.. code-block:: bash

    532 RIPE NCC RPKI Validator/3.1-2020.01.13.09.31.26
    515 reqwest/0.9.19
    190 Jetty/9.4.15.v20190215
    101 RIPE NCC RPKI Validator/3.1-2019.12.16.15.18.18
     81 Routinator/0.7.0
    ...

Such a report can be produced using comands like those below:

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

Streaming to Elasticsearch
--------------------------

.. note:: The examples below require Krill Manager v0.2.2 or higher.

Using the Fluentd support integrated into Krill Manager you can stream logs to
3rd party log analysis tools such as `EFK <https://www.digitalocean.com/community/tutorials/how-to-set-up-an-elasticsearch-fluentd-and-kibana-efk-logging-stack-on-kubernetes>`_
(Elasticsearch, Fluentd and Kibana).

When streaming to an external service you can either do that:

  - Instead of streaming to an S3 storage backend: replace ``s3.conf``.
  - In addition to streaming to an S3 storage backend: modify ``s3.conf`` and add
    additional Fluentd config files.

Below is an example configuration for sending rsync access logs to
Elasticsearch:

.. parsed-literal::

   # elastic-search.conf
   <label @ready>
     <filter \*\*>
       @type grep
       <regexp>
         key container
         pattern /krill_rsyncd\..+/
       </regexp>
     </filter>

     <filter \*\*>
       # Given a log record with a message field whose value is like:
       #   2020/05/11 23:59:59 [31881] connect from UNDETERMINED (105.16.160.2)
       @type parser
       key_name message
       reserve_data true
       <parse>
         @type regexp
         expression /^(?<datetime>\d+\/\d+\/\d+ \d+:\d+:\d+) \[(?<unknown>[^]]*)\] connect from (?<client_host>[^ ]+) \((?<client_ip>[^)]*)\)$/
       </parse>
     </filter>

     <match \*\*>
       @type elasticsearch
       host elasticsearch.mydomain.com
       port 9200
       logstash_format true
     </match>
   </label>

A similar technique can be used to stream NGINX access logs, using the built-in
``nginx`` parser in Fluentd. However, if you use a CDN (content delivery
network) in front of your Krill Manager instance(s) you'll want to analzye the
CDN provider logs, not the NGINX logs.

To stream rsync access logs to Elasticsearch but also still upload all logs to
an S3 compatible storage target, use a copy configuration like so:

.. parsed-literal::

   # copy.conf
   <label @ready>
     <match \*\*>
       @type copy
       <store>
         @type relabel
         @label @s3
       </store>
       <store>
         @type relabel
         @label @elastic-search
       </store>
     </match>
   </label>

   # elasticsearch.conf
   <label @elastic-search>
     # the remainder of this file is the same as above
   </label>

   # s3.conf
   <label @s3>
     # the remainder of this file is the same as the stock s3.conf file
     # that comes with Krill Manager.
   </label>

Installing Additional Fluentd Plugins
-------------------------------------

Krill Manager comes with the following Fluentd plugins pre-installed:

- fluent-plugin-elasticsearch
- fluent-plugin-prometheus
- fluent-plugin-rewrite-tag-filter
- fluent-plugin-s3
- fluent-plugin-systemd

.. note:: The Elasticsearch plugin is included with Krill Manager from v0.2.2.

.. code-block:: bash

   $ CONTAINER_ID=$(sudo docker ps -q --filter "name=krill_log_uploader")
   $ sudo docker exec -it ${CONTAINER_ID} /bin/bash
   # gem install fluent-plugin-XXX
   # exit
   $ sudo docker commit ${CONTAINER_ID} krillmanager/log-streamer:custom
   $ sudo docker service update krill_log_uploader --image krillmanager/log-streamer:custom

.. warning:: An upgrade of Krill Manager may cause the service to revert to
             a stock Krill Manager image. Repeat the steps above to re-install
             the missing plugin. You can also request inclusion of the plugin
             in the next Krill Manager release by submitting an issue to the
             `Krill Manager GitHub issue tracker <https://github.com/NLnetLabs/krillmanager/issues/new/choose>`_.
