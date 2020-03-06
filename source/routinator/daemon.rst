.. _doc_routinator_daemon:

Running as a Daemon
===================

Routinator can run as a service that periodically fetches RPKI data, verifies it
and makes the resulting data set available via the RPKI-RTR protocol and through
the built-in HTTP server. You can start the Routinator service using the
``server`` sub-command.

The HTTP Service
----------------

The CSV, JSON, OpenBGPD and RPSL formats that Routinator can produce in
interactive mode are available via HTTP if the application is running as a
service. You can also check the RPKI origin validation status of a specific BGP
announcement at the ``/validity`` endpoint by supplying the ASN and prefix.

The HTTP server is not enabled by default for security reasons, nor does it have
a default host or port. In order to start the HTTP server at 192.0.2.13 and
2001:0DB8::13 on port 8323, run this command:

.. code-block:: bash

   routinator server --http 192.0.2.13:8323 --http [2001:0DB8::13]:8323

The application will stay attached to your terminal unless you provide the
``-d`` (for daemon) option. After fetching and validating the data set, the
following paths are available:

:/csv:
     Returns the current set of VRPs in csv output format

:/json:
     Returns the current set of VRPs in json output format

:/openbgpd:
     Returns the current set of VRPs in openbgpd output format

:/rpsl:
     Returns the current set of VRPs in rpsl output format

:/validity:
     Returns the RPKI origin validation status of a specific BGP announcement by
     supplying the ASN and prefix in the path, e.g.
     ``/validity?asn=12654&prefix=93.175.147.0/24``

Please note that this server is intended to run on your internal network and
doesn't offer HTTPS natively. If this is a requirement, you can for example run
Routinator  behind an `nginx <https://www.nginx.com>`_ reverse proxy.

Lastly, the HTTP server provides paths that allow you to monitor Routinator
itself and the data it processes, so it may be desirable to have HTTP running
alongside the RTR server. For more information, please refer to the
:ref:`doc_routinator_monitoring` section.

The RTR Service
---------------

Routinator supports RPKI-RTR as specified in :rfc-reference:`8210` as well as
the older version described in :rfc-reference:`6810`.

When launched as an RTR server, routers with support for route origin validation
(ROV) can connect to Routinator to fetch the processed data. This includes
hardware  routers such as `Juniper
<https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin
-as-validation.html>`_, `Cisco
<https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/
15-s/irg-15-s-book/irg-origin-as.html>`_ and `Nokia
<https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%
2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`_, as well as
software solutions like `BIRD <https://bird.network.cz/>`_, `GoBGP
<https://osrg.github.io/gobgp/>`_ and :ref:`others <doc_rpki_rtr>`. The
processed  data is also available in a number of useful output formats, such as
CSV, JSON, RPSL and a format specifically for `OpenBGPD <http://openbgpd.org>`_.

Like the HTTP server, the RTR server is not started by default, nor does it have
a default host or port. Thus, in order to start the RTR server at 192.0.2.13 and
2001:0DB8::13 on port 3323, run this command:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --rtr [2001:0DB8::13]:3323

Please note that port 3323 is not the IANA-assigned default port for the
protocol,  which would be 323. But as this is a privileged port, you would need
to be running Routinator as root when otherwise there is no reason to do that.
The application will stay attached to your terminal unless you provide the
``-d`` (for daemon) option.

By default, the repository will be updated and re-validated every 10 minutes.
You  can change this via the ``--refresh`` option and specify the interval
between  re-validations in seconds. That is, if you rather have Routinator
validate every  15 minutes, the above command becomes:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --rtr [2001:0DB8::13]:3323 --refresh=900

Communication between Routinator and the router using the RPKI-RTR protocol is
done via plain TCP. Below, there is an explanation how to secure the transport
using either SSH or TLS.

.. _doc_routinator_rtr_secure_transport:

Secure Transports
"""""""""""""""""

These instructions were contributed by `wk on Github <https://github.com/NLnetLabs/routinator/blob/master/doc/transports.md>`_.

`RFC 6810 <https://tools.ietf.org/html/rfc6810#page-17>`_ defines a number of
secure transports for RPKI-RTR that can be used to secure communication
between a router and a RPKI relying party.

However, the RPKI Router Implementation Report documented in `RFC 7128
<https://tools.ietf.org/html/rfc7128#page-7>`_ suggests these secure transports
have not been widely implemented. Implementations, however, do exist, and a
secure transport could be valuable in situations where the RPKI relying party is
provided as a public service, or across a non-trusted network.

SSH Transport
+++++++++++++

SSH transport for RPKI-RTR can be configured with the help of `netcat
<http://netcat.sourceforge.net/>`_ and `OpenSSH <https://www.openssh.com/>`_.

Begin by installing the ``openssh-server`` and ``netcat`` packages.

Make sure Routinator is running as an RTR server on localhost:

.. code-block:: bash

   routinator server --rtr 127.0.0.1:3323

Create a username and a password for the router to log into the host with,
such as ``rpki``.

Configure OpenSSH to expose an ``rpki-rtr`` subsystem that acts as a proxy
into Routinator by editing the ``/etc/ssh/sshd_config`` file or equivalent to
include the following line:

.. code-block:: text

   # Define an `rpki-rtr` subsystem which is actually `netcat` used to proxy STDIN/STDOUT to a running `routinator rtrd -a -l 127.0.0.1:3323`
   Subsystem       rpki-rtr        /bin/nc 127.0.0.1 3323

   # Certain routers may use old KEX algos and Ciphers which are no longer enabled by default.
   # These examples are required in IOS-XR 5.3 but no longer enabled by default in OpenSSH 7.3
   Ciphers +3des-cbc
   KexAlgorithms +diffie-hellman-group1-sha1

Restart the OpenSSH server daemon.

An example router-side configuration for a device running IOS-XR:

.. code-block:: bash

   router bgp 65534
    rpki server 192.168.0.100
     username rpki
     password rpki
     transport ssh port 22


TLS Transport
+++++++++++++

TLS transport for RPKI-RTR can be configured with the help of `stunnel
<https://www.stunnel.org/>`_.

Begin by installing the ``stunnel`` package.

Make sure Routinator is running as an RTR server on localhost:

.. code-block:: bash

   routinator server --rtr 127.0.0.1:3323

Acquire (via for example `letsencrypt <https://letsencrypt.org/>`_) or generate
an SSL certificate. In the example below, an SSL certificate for
the domain ``example.com`` generated by ``letsencrypt`` is used.

Create an stunnel configuration file by editing ``/etc/stunnel/rpki.conf``
or equivalent:

.. code-block:: text

   [rpki]
   ; Use a letsencrypt certificate for example.com
   cert = /etc/letsencrypt/live/example.com/fullchain.pem
   key = /etc/letsencrypt/live/example.com/privkey.pem

   ; Listen for TLS rpki-rtr on port 323 and proxy to port 3323 on localhost
   accept = 323
   connect = 127.0.0.1:3323

Restart ``stunnel`` to complete the process.
