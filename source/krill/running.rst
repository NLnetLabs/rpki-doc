.. _doc_krill_xrunning:

Running Krill
=============

Krill has an embedded web server, and saves its status on disk
as json files. Krill does not depend on any database, but will
need to be configured and told where on the system its working
directory is.

You can have a look at all possible configuration directives in
the packaged version of the default configuration file. This file
can be found, relative to where you cloned krill, under: ``./daemon/defaults/krill.conf``.

You will notice that this file contains comments only. I.e. it
documents default configuration settings. In order to override
the Krill config you should create your own ``krill.conf`` file
and use the ``--config`` directive when you start ``krilld``:


.. code-block:: bash

   krilld --config <path-to-config>


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
Letsencrypt, is well documented for these servers.
 

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











