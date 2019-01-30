.. _doc_rpki_rtr:

Router Configuration
====================

Several router vendors participated in the development of the RPKI standards in the IETF, ensuring the technology offered an end-to-end solution for route origin validation. The RPKI to Router protocol (RPKI-RTR), standardised in `RFC 6810 <https://tools.ietf.org/html/rfc6810>`_, was specifically designed to deliver validated prefix origin data to routers. This, as well as origin validation functionality, is currently available in the following platforms:

Juniper
   Junos version 12.2 and newer (`Documentation <https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html>`__)
      
Cisco
   IOS release 15.2 and newer, as well as Cisco IOS/XR since release 4.3.2. (`Documentation <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/15-s/irg-15-s-book/irg-origin-as.html>`__)
   
Nokia
   Release R12.0R4 and newer, running on the 7210 SAS, 7750 SR, 7950 XRS and the VSR. (`Documentation <https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`__)   
   
In addition, various software solutions have support for RPKI as well, such as `BIRD
<https://bird.network.cz/>`_ and `OpenBGPD <http://openbgpd.org>`_. In some cases, such as OpenBGPD, RPKI-RTR is not available but the same result can be achieved through a static configuration. The router will periodically fetch the Validated ROA Payload and allow operators to set up route maps based on the result.

.. Juniper
.. -------

.. ``sample config``

.. Cisco
.. -----

.. ``sample config``

.. Nokia
.. -----

.. ``sample config``

