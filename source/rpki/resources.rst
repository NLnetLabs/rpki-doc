.. _doc_rpki_resources:

Resources
=========

This page provides an overview of all well known open source projects that support RPKI. It includes tools, statistics, measurements projects and presentations about operational experiences. Finally, there is an overview of all work in the Internet Engineering Task Force relevant to RPKI.

Relying Party Software
----------------------

`OctoRPKI <https://github.com/cloudflare/cfrpki#octorpki>`_
   Cloudflare's Relying Party software, written in the Go programming language.

`rcynic <https://github.com/dragonresearch/rpki.net>`_
   "Cynical rsync", software to fetch and validate RPKI certificates by Dragon
   Research Labs, written in the Python programming language.

`RIPE NCC RPKI Validator <https://www.ripe.net/manage-ips-and-asns/resource-management/certification/tools-and-resources>`_
   Full-featured RPKI relying party software, written by the RIPE NCC 
   in the Java programming language.

`Routinator <https://nlnetlabs.nl/projects/rpki/routinator/>`_
   RPKI relying party software written by NLnet Labs in the Rust programming language,
   designed to have a small footprint and great portability.

`RPSTIR <https://github.com/bgpsecurity/rpstir/>`_
   Relying Party Security Technology for Internet Routing (RPSTIR) software, 
   initially written by Raytheon BBN Technologies in the C programming language,
   now maintained by ZDNS.

Certificate Authority Software
------------------------------

`Krill <https://nlnetlabs.nl/projects/rpki/krill/>`_
   RPKI Certificate Authority software by NLnet Labs, written in the Rust 
   programming language. Available late 2019.

`rpkid <https://github.com/dragonresearch/rpki.net>`_
   RPKI Certificate Authority software by Dragon Research Labs, written in 
   the Python programming language.

Supporting Tools
----------------

`BGP-SRx <https://www.nist.gov/services-resources/software/bgp-secure-routing-extension-bgp-srx-prototype>`_
   SRx is an open source reference implementation and research platform by the 
   National Institute for Standards and Technology (NIST). It is intended for 
   investigating emerging BGP security extensions and supporting protocols such 
   as RPKI Origin Validation and BGPSec Path Validation.

`GoRTR <https://github.com/cloudflare/gortr>`_
   An open-source implementation of RPKI to Router protocol (RFC 6810)
   using the the Go programming language. This project is maintained by Louis 
   Poinsignon at Cloudflare.

:ref:`rtrlib`
   The RTRlib implements the client-side of the RPKI-RTR protocol (RFC
   6810, RFC 8210) and BGP Prefix Origin Validation (RFC 6811). This also
   enables the maintenance of router keys, which are required to
   deploy BGPSec.
   
   RTRlib was originally founded by researchers from the Computer Systems & Telematics
   group at Freie Universität Berlin and reseachers from the INET research group at
   Hamburg University of Applied Sciences, under the supervision of Matthias Wählisch
   and Thomas Schmidt. It is now a community project.

`pmacct <http://pmacct.net>`_
   pmacct is a small set of multi-purpose passive network monitoring tools. 
   It can account, classify, aggregate, replicate and export forwarding-plane 
   data, i.e. IPv4 and IPv6 traffic; collect and correlate control-plane data 
   via BGP and BMP; collect and correlate RPKI data; collect infrastructure 
   data via Streaming Telemetry.
   
   The pmacct toolset can perform RPKI Origin Validation and present
   the outcome as a property in the flow aggregation process. Because it
   separates out the various types kinds of (invalid) BGP announcements, 
   operators can a good grasp on how their connectivity to the rest of the
   Internet would look like after deploying a "invalid == reject" policy.

Insights and Statistics
-----------------------

There are several initiatives that measure the adoption and data quality of RPKI:

- `Global country stats, with AS and IP prefix analysis <https://www.nlnetlabs.nl/projects/rpki/rpki-analytics/>`_, by NLnet Labs
- `Cirrus Certificate Transparency Log <https://ct.cloudflare.com/logs/cirrus>`_, by Cloudflare
- `Global certificate and ROA statistics <http://certification-stats.ripe.net>`_, by RIPE NCC
- `RPKI Deployment Monitor <https://rpki-monitor.antd.nist.gov>`_, by NIST
- `The RPKI Observatory <https://nusenu.github.io/RPKI-Observatory/>`_, by nusenu
- `RPKI connection test <http://sg-pub.ripe.net/jasper/rpki-web-test/>`_, by RIPE Labs

Operational Experiences
-----------------------

`Dropping RPKI invalid routes in a service provider network <https://www.youtube.com/watch?v=DkUZvlj1wCk>`_
   Lightning talk by Nimrod Levy - AT&T, NANOG 75, February 2019
   
`RPKI and BGP: our path to securing Internet Routing <https://blog.cloudflare.com/rpki-details/>`_
   Blog post by Jérôme Fleury & Louis Poinsignon - Cloudflare, September 2018
   
`RPKI For Managers <https://www.youtube.com/watch?v=vrzl__yGqLE>`_
   Presentation by Niels Raijer - Fusix Networks, NLNOG Day 2018, September 2018

IETF Documents
--------------

Most of the original work on RPKI standardisation for both origin and path validation was done in the `Secure Inter-Domain Routing (sidr) <https://tools.ietf.org/wg/sidr/>`_ working group. After the work was completed, the working group was concluded.

Since then, the `SIDR Operations (sidrops) <https://tools.ietf.org/wg/sidrops/>`_ working group was formed. This working group develops guidelines for the operation of SIDR-aware networks, and provides operational guidance on how to deploy and operate SIDR technologies in existing and new networks.

All relevant drafts and standards can be found in the archives of these two working groups, with a few exceptions, such as `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_, `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_ and `draft-ietf-grow-rpki-as-cones <https://tools.ietf.org/html/draft-ietf-grow-rpki-as-cones>`_.
