.. _doc_krill_xrunning:

Running Krill
=============

Krill has an embedded web server, and saves its status on disk
as json files. Krill does not depend on any database, but will
need to be configured and told where on the system its working
directory is.

You can have a look at all possible configuration directives in
the packaged version of the default configuration file. This file
can be found, relative to where you cloned krill, under: ``./defaults/krill.conf``.

You will notice that this file contains comments only. I.e. it
documents default configuration settings. In order to override
the Krill config you should create your own ``krill.conf`` file
and use the ``--config`` directive when you start ``krill``:


.. code-block:: bash

   krill --config <path-to-config>


Admin Token
-----------

You will need to generate your own secret token (password) before
you can run krill. If you do not supply a token, Krill will refuse
to start and complain with the following message:

.. code-block:: text

   You MUST provide a value for the master API key, either by setting "auth_token" in the 
   config file, or by setting the KRILL_AUTH_TOKEN environment variable.

There is no default token, because we want to avoid that people
run with default passwords. So, make up a nice one, and either
add it to your config file, or use the ENV variable if you prefer.


Proxy and HTTPS
---------------

Krill uses HTTPS and refuses to do plain HTTP. In theory Krill
should be able to use a key pair and corresponding certificate
signed by a web TA. However, this is untested.

By default Krill will generate a 2048 bit RSA key and self-signed
certificate when it's first started.

We recommend, at least for now, that you run Krill with this
default, and use a proxy server such as nginx if you intend to
make Krill available to the internet. Industry standard proxy
servers such as nginx are much better suited to deal with the
sometimes-not-so-well-meaning people on the internet, and implement
best practices regarding HTTPS.

Also, setting up a widely accepted HTTPS certificate, e.g. through
Let's Encrypt, is well documented for these servers.


Minimal Configuration
---------------------

Krill uses defaults that are sensible for development. Some of these
may also be fine if you are testing Krill locally. However you should
review the following.

Public IP and Port
""""""""""""""""""

We recommend that you keep the following settings, and use a proxy server
if you want to expose Krill to other users:

.. code-block:: text

   # Specify the ip address and port number that the server will use.
   #ip             = "localhost"
   #port           = 3000


Data
""""

By default Krill will expect a directory called ``data`` relative to its
working directory:

.. code-block:: text

   # Specify the directory where the publication server will store its data.
   # Note that clustering through a shared data directory is not supported.
   # But, we plan to look into a proper clustering solution later.
   #data_dir       = "./data"

You should probably change this to use an absolute path instead.

Krill will create a number of subdirectories under this ``data_dir`` for various
purposes:

+----------------------------+-------------------------------------------------------------------------------+
| Directory                  | Contains                                                                      |
+----------------------------+-------------------------------------------------------------------------------+
| cas                        | The state of all CAs in this Krill instance.                                  |
+----------------------------+-------------------------------------------------------------------------------+
| keys                       |                                                                               |
+----------------------------+-------------------------------------------------------------------------------+
| pubd                       | The state of all publishers in this Krill instance.                           |
+----------------------------+-------------------------------------------------------------------------------+
| repo/rsync/current         | Published RPKI objects that should be made available through an rsync server. |
+----------------------------+-------------------------------------------------------------------------------+
| repo/rrdp                  | Published RPKI objects in RRDP (RFC8182) XML format.                          |
+----------------------------+-------------------------------------------------------------------------------+
| rfc6492/<ca>/sent/<parent> | All messages sent by CA to parent, and replies.                               |
+----------------------------+-------------------------------------------------------------------------------+
| rfc6492/<ca>/rcvd/<child>  | All messages received by CA from child, and replies.                          |
+----------------------------+-------------------------------------------------------------------------------+
| rfc8181/<ca>               | All messages sent by CA to repository, and replies.                           |
+----------------------------+-------------------------------------------------------------------------------+
| rfc8181/<publisher>        | All messages received by repository from publisher, and replies.              |
+----------------------------+-------------------------------------------------------------------------------+
| ssl                        | The HTTPS key pair and certificate.                                           |
+----------------------------+-------------------------------------------------------------------------------+


Service and Certificate URIs
""""""""""""""""""""""""""""

The ``rsync_base`` setting should match the URI of your rsync server, as this is put on
any certificates, manifests and ROAs that Krill will create. The ``service_uri`` setting
is used to determine the URI for the RRDP notification file used on certificates, but it's
also used to determine the public URI that will be included in responses to delegated
remote child CAs that you may delegate resources to:

.. code-block:: text

   # Specify the base rsync repository for this server. Publishers will get
   # a base URI that is based on the 'publisher_handle' in the XML file.
   #
   # Note, you should set up an rsync daemon to expose $data_dir/rsync to serve
   # this data. The uri defined here should match the module name in your rsync
   # configuration.
   #rsync_base     = "rsync://localhost/repo/"

   # Specify the base public URI to this service. Other URIs will be derived
   # from this:
   #  <BASE_URI>api/v1/...                (api)
   #  <BASE_URI>rfc8181                   (for remote publishers)
   #  <BASE_URI>rfc6492                   (for remote children)
   #  <BASE_URI>rrdp/..                   (override with rddp_service_uri)
   #  <BASE_URI>ta/ta.cer                 (on TAL for embedded TA)
   #
   # MUST end with a slash.
   #service_uri  = "https://localhost:3000/"

   # Use the following if you want to use another public URI to access the RRDP files,
   # e.g. because you serve them as raw files from another machine with a web server.
   #rrdp_service_uri = "service_uri/rrdp"


Embedded Trust Anchor
---------------------

For testing purposes you may want to run Krill with an embedded
test Trust Anchor (TA). Using a TA will allow you to create your
own test Certificate Authority (CA) and with a locally signed
certificate. This is useful when learning how to deploy and use
Krill.

To use the embedded TA add the following line to your ``krill.conf`` file:

.. code-block:: text

   use_ta = true

The Trust Anchor Locator (TAL) for this TA can be retrieved from
Krill at: ``https://<yourhost>/ta/ta.tal``

You can use this TAL in a Relying Party (RP) tool, such as routinator, to
validate the ROAs you create. But, note that no one else will have this
TAL, so this is useful for testing only.

At this moment there is no way to disable the embedded TA once
it's created. We may add this later, but for now we recommend that
you use this option only on instances that you are prepared to use
for testing only.
