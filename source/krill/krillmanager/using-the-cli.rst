.. _doc_krill_manager_using_the_cli:

Using the CLI
=============

Krill Manager is controlled via a command line interface aka the CLI. The CLI
can be invoked using the ``krillmanager`` command.

.. _krillc:

Krillc
------

Krill has its own CLI known as :ref:`krillc <doc_krill_using_cli>`, but because
Krill is deployed as a Docker container when using Krill Manager, the ``krillc``
program is not available on the host without invoking a complicated Docker
command or making a shell alias to invoke it. Even then you must set environment
variables or command line arguments to tell ``krillc`` where to find the Krill
server and which authentication token to use.

To simplify this, Krill Manager makes available a ``krillc`` command which
invokes the actual :ref:`krillc <doc_krill_using_cli>` program and already
configures it with the correct server URI and authentication token so that you
don't have to. To use this ``krillc`` wrapper just run the ``krillc`` command
on the host. Alternatively you can use the ``krillmanager krillc`` command
(see below).

Usage Summary
-------------

The following summary can be obtained at any time with the ``krillmanager --help`` or ``krillmanager help``
commands:

.. parsed-literal::

   # krillmanager --help
   
   Usage: COMMAND [ARGUMENTS]
   
   A tool for managing NLnet Labs Krill and related services.
   
   Commands:
     :ref:`backup<cmd_backup>`   Backup Krill and supporting services state
     :ref:`certs<cmd_certs>`    List the TLS certificates in use by NGINX
     :ref:`help<cmd_help>`     Display this message
     :ref:`init<cmd_init>`     (Re)initialize DNS, TLS and Krill settings
     :ref:`krillc<cmd_krillc>`   Execute Krill CLI commands
     :ref:`logs<cmd_logs>`     Show the service container logs
     :ref:`renew<cmd_renew>`    Renew expiring NGINX Lets Encrypt certificates
     :ref:`restart<cmd_restart>`  Restart Krill and supporting services
     :ref:`restore<cmd_restore>`  Restore Krill and supporting services state from a backup
     :ref:`start<cmd_start>`    Start Krill and supporting services
     :ref:`status<cmd_status>`   Show the status of the service containers
     :ref:`stop<cmd_stop>`     Stop Krill and supporting services
     :ref:`upgrade<cmd_upgrade>`  Upgrade Krill and supporting services

Querying the Version
--------------------

.. code-block:: text

   # krillmanager --version
   v0.1.3 [Krill: v0.5.0]

This tells you that Krill Manager is version 0.1.3, and that it deploys version
0.5.0 of Krill.

.. _cmd_backup:

Command: backup
---------------

Creates a tar archive on the host filesystem containing all configuration files
and data for Krill Manager and the components that it manages. This includes
NGINX certificate files and Krill embedded repository data files. It does **NOT**
include log files.

The path to the created archive will be printed to the terminal on completion
of the backup. The backup archive can be restored later using the
:ref:`krillmanager restore<cmd_restore>` command.

.. warning:: In order to avoid impacting your system the archive is made while
             all applications are running. There is a very small chance that a
             Krill data file will be inconsistently captured in the backup.

.. _cmd_certs:

Command: certs
--------------

TO DO

.. _cmd_help:

Command: help
-------------

Displays the usage summary.

.. _cmd_init:

Command: init
-------------

Runs the (re)configuration wizard. See :ref:`doc_initial_setup`.

.. _cmd_krillc:

Command: krillc
---------------

See :ref:`krillc` above.

.. _cmd_logs:

Command: logs
-------------

TO DO

.. _cmd_renew:

Command: renew
--------------

TO DO

.. _cmd_restart:

Command: restart
----------------

TO DO

.. _cmd_restore:

Command: restore
----------------

This command restores a backup made previously by the
:ref:`krillmanager backup<cmd_backup>` command.

The restored data will be processed by the current Krill Manager version which
may be newer than the version that created the backup. Any incompatibilities
should be handled by the restore process.

.. note:: If the domain name of the system being restored to is not the same
          as the domain of the system on which the backup was made, you will
          be warned because the certificates will not match the domain being
          served from.

.. _cmd_start:

Command: start
--------------

TO DO

.. _cmd_status:

Command: status
---------------

TO DO

.. _cmd_stop:

Command: stop
-------------

TO DO

.. _cmd_upgrade:

Command: upgrade
----------------

TO DO
