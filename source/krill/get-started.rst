Getting Started with Krill
==========================

This page describes how to get started with Krill in a common scenario: running
as a child of a Regional or National Internet Registry (RIR or NIR) parent,
using a publication point provided by the parent. In later sections, each
component will be described in more detail.

Configuration
-------------

After the installation has completed, first create a configuration file
containing the following directives, using your own values. You can give this
file any name and store it anywhere, but here we will assume you will use the
name ``krill.conf`` and store it under your Krill data_dir.

.. code-block:: text

  data_dir       = "/path/to/data"
  auth_token     = "your-secret"

Note, you can find a full example configuration file with defaults in `the
GitHub repository
<https://github.com/NLnetLabs/krill/blob/master/defaults/krill.conf>`_.

We recommend that you do **not** make Krill available publicly. You can use
the default where Krill will expose its API on ``https://localhost:3000/`` only.
You do not need to have Krill available externally, unless you mean to provide
certificates or a publication server to third parties.

You can then set up the following environment variables so that you can easily
use the Krill CLI on the same machine where Krill is running:

.. code-block:: bash

  export KRILL_CLI_TOKEN="<your-token>"
  export KRILL_CLI_SERVER="https://localhost:3000/"
  export KRILL_CLI_MY_CA="<your-ca-name>"

For you CA name, you can use alphanumeric characters, dashes and underscores,
i.e. ``a-zA-Z0-9_``.

Note that you can use the CLI from another machine, but then you will need to
set up a proxy server in front of Krill and make sure that it has a real TLS
certificate.

Start and Stop the Daemon
-------------------------

There is no standard script to start / stop Krill yet. We will add this in the
near future.

For now you could use the following example script to start Krill. Make sure to
update the ``DATA_DIR`` variable to your real data directory, and make sure you
saved your ``krill.conf`` file there.

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


Backup and Restore
------------------

To back-up Krill:

* stop Krill
* backup the ``DATA_DIR``
* start Krill

We recommend that you stop Krill because there can be a race condition where
Krill was just in the middle of saving its state after performing a background
operation. We will most likely add a process in future that will allow you to
back up Krill in a consistent state while it is running.

To restore Krill just restore the ``DATA_DIR`` and make sure that you refer to
it in the configuration file that you use for your Krill instance.


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

  * Install the new version of Krill
  * Stop the running Krill instance
  * Start Krill again, using the new binary, and the same configuration

Note that after a restart you may see a message like this in your log file:

.. code-block:: text

  2020-01-28 13:41:03 [WARN] [krill::commons::eventsourcing::store] Could not
  deserialize snapshot json '/root/krill/data/pubd/0/snapshot.json', got error:
  'missing field `stats` at line 296 column 1'. Will fall back to events.

You can safely ignore this message. Krill is telling you that the definition of
a struct has changed and therefore it cannot use the snapshot.json file that it
normally uses for efficiency. Instead it needs to build up the current state by
explicitly re-applying all the events that happened to your CA and/or embedded
publication server.

Setting up Your Certificate Authority
-------------------------------------

So you got Krill running and configured as above. Now it's time to set up your
own Certificate Authority (CA) in Krill. This involves the following steps:

  * Create your CA
  * Retrieve your CA's 'child request'
  * Retrieve your CA's 'publisher request'
  * Upload the 'child request' to your parent
  * Save the 'parent response'
  * Upload the 'publisher request' to your publisher (usually your parent)
  * Save the 'repository response'
  * Update the repository for your CA using the 'repository response'
  * Add the parent using the 'parent response'

.. code-block:: bash

  # Add CA
  krillc add

  # retrieve your CA's 'child request'
  krillc parents myid > child_request.xml

  # retrieve your CA's 'publisher request'
  krillc repo request > publisher_request.xml

Next, upload the XML files to your parent and save the response XML files.

.. code-block:: bash

  # update the repository for you CA using the 'repository response'
  krillc repo update rfc8183 repository_response.xml

  # add the parent using the 'parent response'
  krillc parents add --parent myparent --rfc8183 ./parent-response.xml

Note that you can use any local name for ``--parent``. This is the name that
Krill will show to you. Similarly, Krill will use your local CA name which you
set in the ```KRILL_CLI_MY_CA`` ENV variable. However, the parent response
includes the names (or handles as they are called in the RFC) by which it refers
to itself, and your CA. Krill will make sure that it uses these names in the
communication with the parent. There is no need for these names to be the same.


Managing Route Origin Authorisations
------------------------------------

Krill lets users create Route Origin Authorisations (ROAs), the signed objects
that state which Autonomous System (AS) is authorised to originate one of your
prefixes, along with the maximum prefix length it may have.

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

   # Some comment
     # Indented comment

   A: 10.0.0.0/24 => 64496
   A: 10.1.0.0/16-20 => 64496   # Add prefix with max length
   R: 10.0.3.0/24 => 64496      # Remove existing authorisation

You can then add this to your CA:

       .. code-block:: text

         $ krillc roas update --delta ./roas.txt

If you followed the steps above then you would get an error, because there is no
authorisation for 10.0.3.0/24 => 64496. If you remove the line and submit again,
then you should see no response, and no error.

You can list ROAs in the following way:

       .. code-block:: text

          $ krillc roas list
          10.0.0.0/24 => 64496
          10.1.0.0/16-20 => 64496

Displaying History
------------------

You can show the history of all the things that happened to your CA using the
``history`` command.

       .. code-block:: text

          $ krillc history
          id: ca version: 0 details: Initialised with cert (hash): 973e3e967ecb2a2a409a785d1faf61cf73a66044, base_uri: rsync://localhost:3000/repo/ca/, rpki notify: https://localhost:3000/rrdp/notification.xml
          id: ca version: 1 details: added RFC6492 parent 'ripencc'
          id: ca version: 2 details: added resource class with name '0'
          id: ca version: 3 details: requested certificate for key (hash) '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' under resource class '0'
          id: ca version: 4 details: activating pending key '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' under resource class '0'
          id: ca version: 5 details: added route authorization: '10.1.0.0/16-20 => 64496'
          id: ca version: 6 details: added route authorization: '10.0.0.0/24 => 64496'
          id: ca version: 7 details: updated ROAs under resource class '0' added: 10.1.0.0/16-20 => 64496 10.0.0.0/24 => 64496
          id: ca version: 8 details: updated objects under resource class '0' key: '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' added: 31302e312e302e302f31362d3230203d3e203634343936.roa 31302e302e302e302f3234203d3e203634343936.roa  updated: 48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.crl 48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.mft  withdrawn:
