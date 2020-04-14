.. _doc_krill_manager_using_the_cli:

Using the CLI
=============

Krill Manager is controlled via a command line interface (CLI) tool called
``krillmanager``, separate to the ``krillc`` tool that can be used to manage a
Krill server. This page documents how to use both in the context of a Krill
Manager instance.

.. _krillc:

------
krillc
------

On a Krill Manager machine you can invoke the ``krillc`` command just as if you
had installed Krill yourself. However, what you are actually invoking is a
special wrapper provided by Krill Manager which simplifies and tailors the use
of the ``krillc`` command to the Krill Manager context. You can read more about
this in the :ref:`krillmanager krillc <cmd_krillc>` documentation below.

------------
krillmanager
------------

Krill Manager supports the following commands:

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
   v0.1.4 [Krill: v0.5.0]

This tells you that Krill Manager is version 0.1.4, and that it deploys version
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

----

.. _cmd_certs:

Command: certs
--------------

This command outputs information both about the certificates in use by NGINX,
and the certificates being managed by the Lets Encrypt certbot tool.

----

.. _cmd_help:

Command: help
-------------

Displays the usage summary.

----

.. _cmd_init:

Command: init
-------------

Runs the (re)configuration wizard. See :ref:`doc_initial_setup`.

----

.. _cmd_krillc:

Command: krillc
---------------

This command invokes the Krill CLI tool :ref:`krillc <doc_krill_using_cli>`.

.. tip:: You can also invoke this command as just ``krillc`` without the
         ``krillmanager`` prefix, just like in the :ref:`krillc documentation <doc_krill_using_cli>`.

In a Krill Manager instance there is no ``krillc`` binary installed on the
host. Instead this command runs a throw away Krill Docker container and invokes
the ``krillc`` binary contained within.

Normally invoking ``krillc`` requires also defining environment variables or
passing command line arguments to tell ``krillc`` where Krill is and how to
authenticate with it. With Krill Manager this is taken care of for you
automatically. If needed you can override the defaults using command line
arguments in order to interact with a separate external instance of Krill.

Krill Manager also simplifies the interaction with the host filesystem by
automatically remapping any paths to input files supplied on the command line
so that they work when ``krillc`` accesses them from within the Docker
container.

----

.. _cmd_logs:

Command: logs
-------------

This command outputs the Docker service logs for key Krill Manager
components. If invoked without any arguments it displays a usage tip:

.. code-block:: bash

  # krillmanager logs
  Usage: krillmanager logs <krill|nginx|rsyncd> [-f] [--tail=n]

The ``-f`` argument tells the command to keep following the log output.

The ``--tail`` argument tells the command to show only ``n`` lines of prior log output.

----

.. _cmd_renew:

Command: renew
--------------

This command forces the Lets Encrypt certbot agent to attempt to renew any
Let's Encrypt certificates that it is managing. If the certificates are
renewed the NGINX instances will be signalled to reload the certificate files
without causing any downtime.

.. note:: It shouldn't be necessary to use this command as it is triggered
          automatically once a day.

----

.. _cmd_restart:

Command: restart
----------------

This command is an alias for :ref:`stop<cmd_stop>` followed by
:ref:`start<cmd_start>`.

----

.. _cmd_restore:

Command: restore
----------------

This command restores a backup made previously by the :ref:`backup<cmd_backup>`
command.

The restored data will be processed by the current Krill Manager version which
may be newer than the version that created the backup. Any incompatibilities
should be handled by the restore process.

.. note:: If the domain name of the system being restored to is not the same
          as the domain of the system on which the backup was made, you will
          be warned because the certificates will not match the domain being
          served from.

----

.. _cmd_start:

Command: start
--------------

Deploy all Krill Manager managed components according to the configuration
settings chosen when the :ref:`init<cmd_init>` command was last run.

----

.. _cmd_status:

Command: status
---------------

Display a status report indicating which of the Krill Manager components are
running. It also shows a recap of key URIs that can be used to work with the
Krill Manager instance.

----

.. _cmd_stop:

Command: stop
-------------

Terminate all Krill Manager components.

.. warning:: This will cause clients to receive connection refused errors.

----

.. _cmd_upgrade:

Command: upgrade
----------------

Check to see if a newer version of Krill Manager is available and if so offer
to upgrade to it.

.. note:: A newer version of Krill Manager doesn't necessarily contain a newer
          version of Krill.