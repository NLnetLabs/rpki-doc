.. _doc_routinator_running:

Running
=======

After the initialisation has completed, you can start using Routinator in several ways.
It can perform RPKI validation as a one-time operation and print a Validated ROA
Payload (VRP) list in various formats, it can return the validity of a specific
announcement, or it can run as a service that periodically fetches RPKI data, verifies
it and makes the resulting data set available via the RPKI-RTR protocol, and via the
built-in HTTP server.

When launched as a daemon, routers with support for route origin validation (ROV) 
can connect to Routinator to fetch the processed data. This includes hardware 
routers such as `Juniper
<https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin
-as-validation.html>`_, `Cisco
<https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/
15-s/irg-15-s-book/irg-origin-as.html>`_ and `Nokia
<https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%
2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`_, as well as
software solutions like `BIRD <https://bird.network.cz/>`_, `GoBGP <https://osrg.github.io/gobgp/>`_ and :ref:`others <doc_rpki_rtr>`. The processed 
data is also available in a number of useful output formats, such as 
CSV, JSON, RPSL and a format specifically for `OpenBGPD <http://openbgpd.org>`_.

These and all other functions of Routinator are accessible on the command
line via sub-commands:

:vrps:
     Fetches RPKI data and produces a Validated ROA Payload (VRP) list in the 
     specified format.

:validate:
     Outputs the RPKI validity for a specific announcement by supplying Routinator
     with an ASN and a prefix. 
     
:server:
     Starts the daemon that periodically fetches and verifies RPKI data, after
     which it exposes the VRPs via RPKI-RTR, HTTP, or both.

Printing a List of VRPs
-----------------------

Routinator can produce a Validated ROA Payload (VRP) list in five different formats,
which are either printed to standard output or saved to a file:

:csv: 
      The list is formatted as lines of comma-separated values of the prefix in
      slash notation, the maximum prefix length, the autonomous system number, 
      and an abbreviation for the trust anchor the entry is derived from. The 
      latter is the name of the TAL file  without the extension *.tal*. This is 
      the default format used if the ``-f`` option is missing.
:csvext: 
      This is an extended version of the *csv* format, which was used by the RIPE
      NCC RPKI Validator 1.x. Each line contains these comma-separated values: the
      rsync URI of the ROA the line is taken from (or "N/A" if it isn't from a ROA),
      the autonomous system number, the prefix in slash notation, the maximum prefix
      length, the not-before date and not-after date of the validity of the ROA.
:json:
      The list is placed into a JSON object with a single  element *roas* which
      contains an array of objects with four elements each: The autonomous system 
      number of  the  network  authorised to originate a prefix in *asn*, the prefix
      in slash notation in *prefix*, the maximum prefix length of the announced route
      in *maxLength*, and the trust anchor from which the authorisation was derived 
      in *ta*. This format is identical to that produced by the RIPE NCC Validator 
      except for different naming of the trust anchor. Routinator uses the name 
      of the TAL file without the extension *.tal* whereas the RIPE NCC Validator 
      has a dedicated name for each.
:openbgpd:
      Choosing  this format causes Routinator to produce a *roa-set*
      configuration item for the OpenBGPD configuration.
:rpsl:
      This format produces a list of RPSL objects with the authorisation in the
      fields *route*, *origin*, and *source*. In addition, the fields *descr*,
      *mnt-by*, *created*, and *last-modified*, are present with more or less
      meaningful values.
:summary:
      This format produces a summary of the content of the RPKI repository. For
      each trust anchor, it will print the number of verified ROAs and VRPs. Note
      that this format does not take filters into account. It will always provide
      numbers for the complete repository.

For example, to get a file with with the validated ROA payload in JSON format, run:

.. code-block:: bash

   routinator vrps --format json --output authorisedroutes.json

Filtering
"""""""""

In case you are looking for specific information in the output, Routinator allows
filtering to see if a prefix or ASN is covered or matched by a VRP. You can do this
using the ``--filter-prefix`` and ``--filter-asn`` flags. 

When using ``--filter-prefix``, the result will include VRPs regardless of their
ASN and MaxLength. Both filter flags can be combined and used multiple times in a 
single query and will be treated as a logical *"or"*. When using ``--filter-asn``,
you can use both ``AS64511`` and ``64511`` as the notation.

A validation run will be started before returning the result, making sure you get the
latest information. If you would like a result from the current cache, you can use the ``--noupdate`` or ``-n`` flag.
.. code-block:: bash

   routinator vrps --filter-prefix 93.175.146.0/24
   ASN,IP Prefix,Max Length,Trust Anchor
   AS12654,93.175.146.0/24,24,ripe

.. code-block:: bash

   routinator vrps --filter-asn 196615
   ASN,IP Prefix,Max Length,Trust Anchor
   AS196615,2001:7fb:fd03::/48,48,ripe
   AS196615,93.175.147.0/24,24,ripe


Validity Checker
----------------

You can check the RPKI origin validation status of a specific BGP announcement using the
``validate`` subcommand and by supplying the ASN and prefix. A validation run will be
started before returning the result, making sure you get the latest information. If you
would like a result from the current cache, you can use the ``--noupdate`` or ``-n`` 
flag.

.. code-block:: bash

   routinator validate --asn 12654 --prefix 93.175.147.0/24
   Invalid

A detailed analysis on the reasoning behind the validation is printed in  JSON format
including lists of the VPRs that caused the particular result.

.. code-block:: json

   routinator validate --json --asn 12654 --prefix 93.175.147.0/24
   {
     "validated_route": {
      "route": {
        "origin_asn": "AS12654",
        "prefix": "93.175.147.0/24"
      },
      "validity": {
        "state": "Invalid",
        "reason": "as",
        "description": "At least one VRP Covers the Route Prefix, but no VRP ASN matches the route origin ASN",
        "VRPs": {
         "matched": [
         ],
         "unmatched_as": [
           {
            "asn": "AS196615",
            "prefix": "93.175.147.0/24",
            "max_length": "24"
           }

         ],
         "unmatched_length": [
         ]      }
      }
     }
   }

If you run the HTTP service, this information is also available at the ``/validity``
endpoint.

Running the HTTP Service
------------------------

The CSV, JSON, OpenBGPD and RPSL formats that Routinator can produce are available
via HTTP if the application is running as a service. The HTTP server is not enabled
by default for security reasons, nor does it have a default host or port. In order
to start the HTTP server at 192.0.2.13 and 2001:0DB8::13 on port 8323, run this
command:

.. code-block:: bash

   routinator server --http 192.0.2.13:8323 --http [2001:0DB8::13]:8323

The application will stay attached to your terminal unless you provide the ``-d`` (for daemon) option. After fetching and validating the data set, the following paths are available:

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

Please note that this server is intended to run on your internal network and doesn't
offer HTTPS natively. If this is a requirement, you can for example run Routinator 
behind an `nginx <https://www.nginx.com>`_ reverse proxy. 

Lastly, the HTTP server provides paths that allow you to monitor Routinator, so it
may be desirable to have HTTP running alongside the RTR server. For more
information, please refer to the :ref:`doc_routinator_monitoring` section.

Running the RTR Service
-----------------------

Routinator supports RPKI-RTR as specified in `RFC 8210
<https://tools.ietf.org/html/rfc8210>`_ as well as the older version from `RFC 6810 
<https://tools.ietf.org/html/rfc7730>`_. Like the HTTP server, the RTR server is not
started by default, nor does it have a default host or port. Thus, in order to start
the RTR server at 192.0.2.13 and 2001:0DB8::13 on port 3323, run this command:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --rtr [2001:0DB8::13]:3323

Please note that port 3323 is not the IANA-assigned default port for the protocol, 
which would be 323. But as this is a privileged port, you would need to be running
Routinator as root when otherwise there is no reason to do that. The application will
stay attached to your terminal unless you provide the ``-d`` (for daemon) option.

By default, the repository will be updated and re-validated every hour as per the
recommendation in the RFC. You can change this via the ``--refresh`` option and specify
the interval between re-validations in seconds. That is, if you rather have Routinator
validate every 15 minutes, the above command becomes:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --rtr [2001:0DB8::13]:3323 --refresh=900
    
Communication between Routinator and the router using the RPKI-RTR protocol is done
via plain TCP. In the next section, there is an explanation how to secure the transport
using either SSH or TLS.