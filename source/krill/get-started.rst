Getting Started with Krill
==========================

Here we will go through the steps to get started with Krill under an RIR or NIR
parent CA, using a publication point provided by the parent.

Configure Krill
---------------

You will need to create a minimum configuration file for Krill. At a minimum you
should set your admin token, and you probably want to configure the path to Krill's
data directory.

Create a configuration file and set the following directives, using your own
values of course. You can give this file any name and store it anywhere, but
here we will assume you will use the name `krill.conf` and store it under your
Krill data_dir.

.. code-block:: text

  data_dir       = "/path/to/data"
  auth_token     = "your-secret"

Note, you can find a full example config file with defaults `here <https://github.com/NLnetLabs/krill/blob/master/defaults/krill.conf>`_.

We recommend that you do NOT make krill available publicly. I.e. you can use the
default where Krill will expose its API on `https://localhost:3000/` only. You
do not need to have Krill available, unless you mean to provide certificates or
a publication server to third parties.

You can then set up the following environment variables so that you can easily
use the Krill CLI on the same machine where KRILL is running:

.. code-block:: bash

  export KRILL_CLI_TOKEN="<your-token>"
  export KRILL_CLI_SERVER="https://localhost:3000/"
  export KRILL_CLI_MY_CA="<your-ca-name>"

Note, that you *can* use the CLI from another machine, but then you will need to
set up a proxy server in front of Krill and make sure that it has a real HTTPS
certificate.

You can use any name for your 'MY_CA' as long as it contains the only the
following characters: `a-zA-Z0-9_`

Setting this name in an ENV variable saves a lot of typing down the line. In the
somewhat unlikely event where you will need to manage multiple CAs under a single
Krill instance, you can either override the default ca using the `--ca` option
where applicable, or not use the ENV variable.


Start or Stop Krill
-------------------

There is no standard script to start / stop krill yet. We will add this in the
near future.

For now you could use something like the following, naive, script to start krill.
Just update the DATA_DIR variable to your real data directory, and make sure you
saved the your `krill.conf` file there.

.. code-block:: bash

  #!/bin/bash

  export KRILL="krill"
  export DATA_DIR="/path/to/data"
  export KRILL_PID="$DATA_DIR/krill.pid"
  export CONF="$DATA_DIR/krill.conf"
  export SCRIPT_OUT="$DATA_DIR/process.log"

  nohup $KRILL -c $CONF >$SCRIPT_OUT 2>&1 &
  echo $! > $KRILL_PID

And then you can use the following, equally naive script, to stop it:

.. code-block:: bash

  #!/bin/bash

  export DATA_DIR="/path/to/data"
  export KRILL_PID="$DATA_DIR/krill.pid"

  kill `cat $KRILL_PID`


Backup / Restore Krill
----------------------

To back-up Krill we recommend that you:
* stop Krill
* backup the `DATA_DIR`
* start Krill

So, we recommend that you stop Krill, because there can be a race condition where
Krill was just in the middle of saving its state after performing a background
operation. We will most likely add a process in future that will allow you to
back up Krill in a consistent state while it is running.

To restore Krill just restore the `DATA_DIR` and make sure that you refer to it
in the configuration file that you use for your Krill instance.


Disk Space used by Krill
------------------------

Krill stores all of its data under the `DATA_DIR`. For users who will operate a
CA under an RIR / NIR parent the following sub-directories are relevant:

+---------+------------------------------------------------------+
| Dir     | Purpose                                              |
+=========+======================================================+
| ssl     | Contains the https key and cert used by Krill        |
+---------+------------------------------------------------------+
| cas     | Contains the history of your CA in raw json format   |
+---------+------------------------------------------------------+
| rfc6492 | Contains all messages exchanged with your parent     |
+---------+------------------------------------------------------+
| rfc8181 | Contains all messages exchanged with your repository |
+---------+------------------------------------------------------+

The space used by the latter two dirs can grow significantly over time. We think
it may be a good idea to have an audit trail of all these exchanges. But if
space is a concern you can safely archive or delete the contents of these two
directories.

In a future version of Krill we will most likely only store the exchanges where
either an error was returned, or your Krill instance asked for a change to be
done at the parent side: like requesting a new certificate, or publishing an
object. The noise from the periodic exchanges where your CA asks the parent for
its entitlements will then no longer be logged.

Upgrade Krill
-------------

It is our goal that future versions of Krill will continue to work with the
configuration files and saved data from version 0.4.1 and above. However, please
read the Changelog to be sure.

That being said the normal process would be to:
* install the new version of krill
* stop the running Krill instance
* start Krill again, using the new binary, and the same config


Set up your Krill CA
--------------------

So you got Krill running and configured as above. Now it's time to set up your
own Certification Authority (CA) in Krill. This involves the following steps:
* create your CA
* retrieve your CA's 'child request'
* retrieve your CA's 'publisher request'
* upload the 'child request' to your parent
* save the 'parent response'
* upload the 'publisher request' to your publisher (usually your parent)
* save the 'repository response'
* update the repository for you CA using the 'repository response'
* add the parent using the 'parent response'


.. code-block:: bash

  # Add CA
  krillc add

  # retrieve your CA's 'child request'
  krillc parents myid > child_request.xml

  # retrieve your CA's 'publisher request'
  krillc repo request > publisher_request.xml

Then upload the XML files to your parent. And save the response XML files.

.. code-block:: bash

  # update the repository for you CA using the 'repository response'
  krillc repo update rfc8183 repository_response.xml

  # add the parent using the 'parent response'
  krillc parents add --parent myparent --rfc8183 ./parent-response.xml

Note that you can use any local name for `--parent`. This is the name that Krill
will show to you. Similarly Krill will use your local CA name which you set in
the `KRILL_CLI_MY_CA` ENV variable. However, the parent response includes the
names (or handles as they are called in the RFC) by which it refers to itself,
and your CA. Krill will make sure that it uses these names in the communication
with the parent. There is no need for these names to be the same.


ROAs
""""

At this point you probably want to manage some ROAs!

Krill lets users configure Route Authorizations, i.e. the intent to authorise a Prefix you
hold, up to a maximum length to be announced by an ASN. Krill will make sure that the actual
ROA objects are created. Krill will also refuse to accept authorizations for prefixes you
don't hold.


Update ROAs
"""""""""""

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

   # Some comment
     # Indented comment

   A: 10.0.0.0/24 => 64496
   A: 10.1.0.0/16-20 => 64496   # Add prefix with max length
   R: 10.0.3.0/24 => 64496      # Remove existing authorization

You can then add this to your CA:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

         $ krillc roas update --delta ./roas.txt

    .. tab-container:: api
       :title: api

       See: :krill_api_route_post:`POST /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`

If you followed the steps above then you would get an error, because there is no
authorization for 10.0.3.0/24 => 64496. If you remove the line and submit again,
then you should see no response, and no error.


List Route Authorizations
"""""""""""""""""""""""""

You can list Route Authorizations as well:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc roas list
          10.0.0.0/24 => 64496
          10.1.0.0/16-20 => 64496

    .. tab-container:: api
       :title: api

       See: :krill_api_route_get:`GET /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`


History
"""""""

You can show the history of all the things that happened to your CA:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

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

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ca/history <cas~1{ca_handle}~1history>`
