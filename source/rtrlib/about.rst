.. _rtrlib_about:

About
=====

In a Nutshell
-------------

RTRlib is a C library that implements the client side of the RPKI-RTR
protocol as well as route origin validation. Basically, it maintains data
from an RPKI cache server (e.g., Routinator) and allows to verify whether
an autonomous system (AS) is the legitimate origin AS, based on the fetched
valid ROA data. It is prepared for BGPsec path validation.

RTRlib powers RPKI in BGP software routers such as `FRR
<https://frrouting.org/>`_ and is the base for monitoring tools. A Python
binding is available. The basis RTRlib package includes the library and
lightweight command line tools.


Why do I need the RTRlib?
-------------------------

RTRlib gives easy and highly efficient access to cryptographically valid
RPKI data without relying on a specific cache server or RPKI validator
implementation. To prevent single point of failures, it handles failover
between multiple cache servers.

Not only developers of routing software but also network operators benefit
from RTRlib. Developers can integrate the RTRlib into their BGP daemon to
extend their implementation towards RPKI. Network operators may use the
RTRlib to develop monitoring tools (e.g., to evaluate the performance of
caches or to validate BGP data).


License
-------

This software is free, open source and licensed under MIT.


Supported RFCs
--------------

The current version implements `RFC 6811
<https://tools.ietf.org/html/rfc6811>`_ and `RFC 8210
<https://tools.ietf.org/html/rfc8210>`_.


Community
---------

If you run into a problem with RTRlib or you have a feature request, please
create an `issue on Github <https://github.com/rtrlib/rtrlib/issues>`_. We
are also happy to accept your pull requests.  For general discussion and
exchanging operational experiences we provide a `mailing list
<rtrlib@googlegroups.com>`_. More details about RTRlib are available on the
`project website <https://rtrlib.realmv6.org/>`_.
