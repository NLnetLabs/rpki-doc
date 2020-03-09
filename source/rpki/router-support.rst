.. index:: ! Router support

.. _doc_rpki_rtr:

Router Support
==============

Several router vendors participated in the development of the RPKI standards in
the IETF, ensuring the technology offered an end-to-end solution for route
origin validation. The RPKI to Router protocol (RPKI-RTR) is standardised in
:RFC:`6810` (v0) and :RFC:`8210` (v1). Is it specifically
designed to deliver validated prefix origin data to routers. This, as well as
origin validation functionality, is currently available in on various hardware
platforms and software solutions.

Hardware Solutions
------------------

Juniper — `Documentation <https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html>`__ — `Day One Book <https://www.juniper.net/uk/en/training/jnbooks/day-one/deploying-bgp-routing-security/>`_
   Junos version 12.2 and newer

Cisco — `Documentation <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/15-s/irg-15-s-book/irg-origin-as.html>`__
   IOS release 15.2 and newer, as well as Cisco IOS/XR since release 4.3.2.

Nokia — `Documentation <https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`__
   Release R12.0R4 and newer, running on the 7210 SAS, 7750 SR, 7950 XRS and the VSR.


Software Solutions
------------------

Various software solutions have support for origin validation:

- `BIRD <https://bird.network.cz/>`_
- `OpenBGPD <http://openbgpd.org>`_
- `FRRouting <https://frrouting.org/>`_
- `GoBGP <https://osrg.github.io/gobgp/>`_
- `VyOS <https://www.vyos.io>`_

In some solutions, such as OpenBGPD, RPKI-RTR is not available but the same
result can be achieved through a static configuration. The router will
periodically fetch the validated cache and allow operators to set up route maps
based on the result. :ref:`Relying party software<doc_tools>` such as
Routinator and rpki-client can export validated data in a format that OpenBGPD
can parse.

:ref:`rtrlib` is a C library that implements the client side of the RPKI-RTR
protocol, as well as route origin validation. RTRlib powers RPKI in BGP software
routers such as `FRR <https://frrouting.org/>`_. In a nutshell, it maintains
data from RPKI relying party software and allows to verify whether an autonomous
system (AS) is the legitimate origin AS, based on the fetched valid ROA data.
`BGP‑SRx
<https://www.nist.gov/services-resources/software/bgp-secure-routing-extension-bgp-srx-prototype>`_
by NIST is a prototype that can perform similar functions.
