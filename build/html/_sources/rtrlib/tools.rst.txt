.. _rtrlib_tools:

RTRlib Command Line Tools
=========================

The RTRlib software package includes two lightweight command line tools to
showcase some of the RTRlib features.  ``rtr-client`` connects to an RPKI
cache server, fetches and maintains the valid ROA payloads, and prints the
received data. ``rpki-rov`` allows to verify whether an autonomous system
is the legitimate origin AS of an IP prefix, based on RPKI data.

If you want to use these command line tools, you need an RPKI-RTR
connection to an RPKI cache server (e.g., Routinator). For those who do not
have access to a cache server, we provide a public cache with *hostname*
``rpki-validator.realmv6.org`` and *port* ``8282``.


RTRlib RTR Client
-----------------

``rtrclient`` is part of the default RTRlib software package. This command
line tool connects to an RPKI cache server and prints the received valid
ROA payloads to standard out.

To establish a connection to RPKI cache servers, the client can use *TCP*
or *SSH* transport sockets.  To run the program you have to specify the
transport protocol as well as the hostname and port of the RPKI cache
server; additionally you can set several options.  To get a complete
reference over all options for the command simply run ``rtrclient`` in a
shell.

:numref:`lst_rtrlib_rtrclient` shows how to connect the ``rtrclient`` to a cache
server as well as 10 lines of the resulting output. It shows IPv4 and IPv6
prefixes secured by ROAs, the allowed prefix lengths, and the legitimate
origin AS numbers.  Each line represents either a ROA that was added
(``+``) or removed (``-``) from the selected RPKI cache server.  The RTRlib
client will receive and print such updates until the program is terminated,
i.e., by ``ctrl + c``.

.. code-block:: bash
    :caption: Output of the rtrclient tool.
    :name: lst_rtrlib_rtrclient

    rtrclient tcp -k -p rpki-validator.realmv6.org 8282
    Prefix                                     Prefix Length         ASN
    + 89.185.224.0                                19 -  19        24971
    + 180.234.81.0                                24 -  24        45951
    + 37.32.128.0                                 17 -  17       197121
    + 161.234.0.0                                 16 -  24         6306
    + 85.187.243.0                                24 -  24        29694
    + 2a02:5d8::                                  32 -  32         8596
    + 2a03:2260::                                 30 -  30       201701
    + 2001:13c7:6f08::                            48 -  48        27814
    + 2a07:7cc3::                                 32 -  32        61232
    + 2a05:b480:fc00::                            48 -  48        39126



RTRlib ROV Validator
--------------------

``rpki-rov`` is also part of the RTRlib software package.
This simple command line interface allows to verify whether an autonomous
system is allowed to announce a specific IP prefix, based on data received
from an RPKI cache server.

To run the program, you must provide two parameters, ``hostname`` and
``port`` of a known RPKI cache server. Then, you can interactively validate
IP prefixes by typing ``prefix``, ``prefix length``, and ``origin ASN``
separated by spaces. Press ``ENTER`` to run the validation.  The result
will be shown instantly below the input.

.. note:: ``rpki-rov`` can validate IPv4 and IPv6 prefixes by default.

:numref:`lst_rtrlib_rpki-rov` shows the validation results of all `RPKI-enabled
RIPE RIS beacons
<https://www.ripe.net/analyse/internet-measurements/routing-information-service-ris/current-ris-routing-beacons>`_.
The output consists of three columns, which are separated by pipes (``|``):

   ``<input query> | <ROAs> | <validation result>``.

The validation results are 
``0`` for *valid*, ``1`` for *not found*, and ``2`` for *invalid*.
   
In case of a *valid* and *invalid* prefix-AS pair, the output shows the
matching ROAs for the given prefix and AS number.  If multiple ROAs for a
prefix exist, they are listed in a row separated by commas (``,``).

.. code-block:: bash
    :caption: Output of rpki-rov showing validation results of multiple prefixes.
    :name: lst_rtrlib_rpki-rov

    rpki-rov rpki-validator.realmv6.org 8282
    93.175.146.0 24 12654
    93.175.146.0 24 12654|12654 93.175.146.0 24 24|0
    2001:7fb:fd02:: 48 12654
    2001:7fb:fd02:: 48 12654|12654 2001:7fb:fd02:: 48 48|0
    93.175.147.0 24 12654
    93.175.147.0 24 12654|196615 93.175.147.0 24 24|2
    2001:7fb:fd03:: 48 12654
    2001:7fb:fd03:: 48 12654|196615 2001:7fb:fd03:: 48 48|2
    84.205.83.0 24 12654
    84.205.83.0 24 12654||1
    2001:7fb:ff03:: 48 12654
    2001:7fb:ff03:: 48 12654||1




