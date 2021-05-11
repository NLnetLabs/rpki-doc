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

.. Important:: The versions listed here are the earliest ones where RPKI support
               became available. However, a newer version may be required to get
               recommended improvements and bug fixes. Please check your vendor
               documentation and knowledge base.

Juniper — `Documentation <https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html>`_
   Junos version 12.2 and newer. Please read PR1461602 and PR1309944 before deploying.

Cisco — `Documentation <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/15-s/irg-15-s-book/irg-origin-as.html>`_
   IOS release 15.2 and newer, as well as Cisco IOS/XR since release 4.3.2.

Nokia — `Documentation <https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`_
   SR OS 12.0.R4 and newer, running on the 7210 SAS, 7250 IXR, 7750 SR, 7950 XRS and the VSR.

Arista — `Blog post <https://twitter.com/kwf/status/1250598771399901187>`_
   EOS 4.24.0F and newer
   
MikroTik — `RouterOS v7 BETA forum thread <https://forum.mikrotik.com/viewtopic.php?f=1&t=161980>`_ - `RPKI forum thread <https://forum.mikrotik.com/viewtopic.php?t=81340>`_
   7.0beta7 and newer

Huawei - `Documentation <https://support.huawei.com/hedex/hdx.do?lib=EDOC1000142112AEI0520D&docid=EDOC1000142112&lang=en&v=05&tocLib=EDOC1000142112AEI0520D&tocV=05&id=dc_vrp_bgp_cfg_3099&tocURL=resources%2525252Fvrp%2525252Fdc_vrp_bgp_cfg_3099.html&p=t&fe=1&ui=3&keyword=configuring%25252Brpki>`_
   VRP 8.150 and newer. 

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

RTRLib is a C library that implements the client side of the RPKI-RTR
protocol, as well as route origin validation. RTRlib powers RPKI in BGP software
routers such as `FRR <https://frrouting.org/>`_. In a nutshell, it maintains
data from RPKI relying party software and allows to verify whether an autonomous
system (AS) is the legitimate origin AS, based on the fetched validated ROA data.
`BGP‑SRx
<https://www.nist.gov/services-resources/software/bgp-secure-routing-extension-bgp-srx-prototype>`_
by NIST is a prototype that can perform similar functions.
