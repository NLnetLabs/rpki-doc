.. _doc_krill_getting_started:

Getting Started
===============

After the installation has completed, there are just two things you need to
configure before you can start using Krill. First, you will need a a data
directory, which will store everything Krill needs to run. Secondly, you will
need to create a basic configuration file, specifying a secret token and the
location of your data directory.

Configuration
-------------

The first step is to choose where your data directory is going to live and to
create it. In this example we are simply creating it in our home directory.

.. code-block:: bash

  mkdir ~/data

Krill can generate a basic configuration file for you. We are going to specify
the two required directives, a secret token and the path to the data directory,
and then store it in this directory.

.. code-block:: bash

  krillc config simple --token correct-horse-battery-staple --data ~/data/ > ~/data/krill.conf

You can find a full example configuration file with defaults in `the
GitHub repository
<https://github.com/NLnetLabs/krill/blob/master/defaults/krill.conf>`_.

Start and Stop the Daemon
-------------------------

There is currently no standard script to start and stop Krill. You could use the
following example script to start Krill. Make sure to update the
``DATA_DIR`` variable to your real data directory, and make sure you saved
your :file:`krill.conf` file there.

.. code-block:: bash

  #!/bin/bash
  KRILL="krill"
  DATA_DIR="/path/to/data"
  KRILL_PID="$DATA_DIR/krill.pid"
  CONF="$DATA_DIR/krill.conf"
  SCRIPT_OUT="$DATA_DIR/krill.log"

  nohup $KRILL -c $CONF >$SCRIPT_OUT 2>&1 &
  echo $! > $KRILL_PID

You can use the following sample script to stop Krill:

.. code-block:: bash

  #!/bin/bash
  DATA_DIR="/path/to/data"
  KRILL_PID="$DATA_DIR/krill.pid"

  kill `cat $KRILL_PID`

Proxy and HTTPS
---------------

Krill uses HTTPS and refuses to do plain HTTP. By default Krill will generate a
2048 bit RSA key and self-signed certificate in :file:`/ssl` in the data
directory when it is first started. Replacing the self-signed certificate with a
TLS certificate issued by a CA works, but has not been tested extensively.

For a robust solution, we recommend that you use a proxy server such as NGINX or
Apache if you intend to make Krill available to the Internet. Also, setting up a
widely accepted TLS certificate is well documented for these servers.

.. Warning:: We recommend that you do **not** make Krill available publicly.
             You can use the default where Krill will expose its CLI, API and
             UI on ``https://localhost:3000/`` only. You do not need to have
             Krill available externally, unless you intend to provide
             certificates or a publication server to third parties.

Backup and Restore
------------------

To back-up Krill:

- Stop Krill
- Backup your data directory
- Start Krill

We recommend that you stop Krill because there can be a race condition where
Krill was just in the middle of saving its state after performing a background
operation. We will most likely add a process in future that will allow you to
back up Krill in a consistent state while it is running.

To restore Krill just put back your data directory and make sure that you refer
to it in the configuration file that you use for your Krill instance.

Used Disk Space
---------------

Krill stores all of its data under the ``DATA_DIR``. For users who will operate
a CA under an RIR / NIR parent the following sub-directories are relevant:

+---------+------------------------------------------------------+
| Dir     | Purpose                                              |
+=========+======================================================+
| ssl     | Contains the HTTPS key and cert used by Krill        |
+---------+------------------------------------------------------+
| cas     | Contains the history of your CA in raw JSON format   |
+---------+------------------------------------------------------+
| rfc6492 | Contains all messages exchanged with your parent     |
+---------+------------------------------------------------------+
| rfc8181 | Contains all messages exchanged with your repository |
+---------+------------------------------------------------------+

The space used by the latter two directories can grow significantly over time.
We think it may be a good idea to have an audit trail of all these exchanges.
However, if space is a concern you can safely archive or delete the contents of
these two directories.

In a future version of Krill we will most likely only store the exchanges where
either an error was returned, or your Krill instance asked for a change to be
made at the parent side: like requesting a new certificate, or publishing an
object. The periodic exchanges where your CA asks the parent for its
entitlements will then no longer be logged.

Upgrade
-------

It is our goal that future versions of Krill will continue to work with the
configuration files and saved data from version 0.4.1 and above. However, please
read the changelog to be sure.

The normal process would be to:

- Install the new version of Krill
- Stop the running Krill instance
- Start Krill again, using the new binary, and the same configuration

Note that after a restart you may see a message like this in your log file:

.. code-block:: text

  2020-01-28 13:41:03 [WARN] [krill::commons::eventsourcing::store] Could not
  deserialize snapshot json '/root/krill/data/pubd/0/snapshot.json', got error:
  'missing field `stats` at line 296 column 1'. Will fall back to events.

You can safely ignore this message. Krill is telling you that the definition of
a struct has changed and therefore it cannot use the :file:`snapshot.json` file
that it normally uses for efficiency. Instead, it needs to build up the current
state by explicitly re-applying all the events that happened to your CA and/or
embedded publication server.
