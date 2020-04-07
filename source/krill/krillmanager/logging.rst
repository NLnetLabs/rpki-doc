.. _doc_krill_manager_logging:

Logging
=======

------------
Architecture
------------

**Note:** When the cluster only consists of a single node all of the flows
defined below run on the same node.

Standard Out/Error Streaming
----------------------------

Docker Forwarder service on each Krill Manager node receives log events:

::

                Kernel -->|
    Krill                 |
   Manager --> DockerD -->|--> SystemD Journal --> /var/log/journal <-- fluentd --> Forwarder
  Containers              |       |
              Programs -->|       |--> rsyslog --> /var/log/xxx

Docker Processor service on the master Krill Manager node aggregates and
outputs the events received from the forwarders:

::

   Receiver --> fluentd --> Output (none, s3, or your custom configuration)

Krill RFC Audit File upload
---------------------------

Krill on one Krill Manager node:

::

   Krill --> Docker Volume --> Gluster Replicated Store

Docker Log Uploader service on the same or other KrillManager node:

::

   Gluster Replicated Store <-- Docker Volume <-- S3cmd --> S3-like storage service

---------------------
With Logging Disabled
---------------------

When log uploading is disabled the fluentd Forwarder, fluentd Processor and
the Log Uploader services are not created. Logs are handled by DockerD,
JournalD and rsyslog as usual.

--------------------
With Logging Enabled
--------------------

The fluentd Forwarder, fluentd Processor and Log Uploader services are active
and process the logs collected from the journal on each Krill Manager node and
any RFC audit files created by Krill.

Upload Frequency
----------------

RFC protocol exchange logs are uploaded hourly. All other logs are uploaded at
least every 10 minutes, more frequently if there is a lot of logging activity.

Log Retention
-------------

When log upload is enabled, local copies of Krill RFC audit logs are deleted
after two days as these logs can become quite large. All other logs are
rotated according to the O/S systemd journal behaviour and logrotate
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

Files in ``/fluentd-conf/*.conf`` can be used to configure fluentd according to
your own design, streaming logs to any of the many 3rd party services that
fluentd supports.

To force fluentd to reload the configuration either restart all services with
``krillmanager restart`` or only the fluentd processor service with
``docker service restart krill_log_uploader --force``.

To force fluentd to flush its buffers you can use the
``docker kill -s SIGUSR1 <container name/id>`` command on the node where the
``krill_log_uploader`` container is running.

.. Tip::
   See also:
     - `fluentd: List of Data Outputs <fluentd.org/dataoutputs>`_
     - `fluentd: Input / Output Plugins <https://www.fluentd.org/plugins/all#input-output>`_

Customising Audit Log Upload
----------------------------

The ``/s3cmd-conf/s3cmd.conf`` file can be edited to take advantage of any additional
features of your S3-like service provider that s3cmd supports.

.. Tip::
   See also:
     - `About the s3cmd configuration file <https://s3tools.org/kb/item14.htm>`_
