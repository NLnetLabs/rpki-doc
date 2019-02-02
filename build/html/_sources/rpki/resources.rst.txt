.. _doc_rpki_resources:

Resources
=========

This page provides an overview of all well known open source projects that support RPKI. It includes tools, statistics and measurements projects in alphabetical order.

Relying Party Software
----------------------

`rcynic <https://github.com/dragonresearch/rpki.net>`_
   "Cynical rsync", software to fetch and validate RPKI certificates, written
   in the Python programming language.

`RIPE NCC RPKI Validator <https://www.ripe.net/manage-ips-and-asns/resource-management/certification/tools-and-resources>`_
   Full-featured RPKI relying party software written by the RIPE NCC 
   in the Java programming language.

`Routinator <https://nlnetlabs.nl/projects/rpki/routinator/>`_
   RPKI relying party software written by NLnet Labs in the Rust programming language,
   designed to have a small footprint and great portability.

`RPSTIR <https://github.com/bgpsecurity/rpstir/>`_
   Relying Party Security Technology for Internet Routing (RPSTIR) software
   by Raytheon BBN Technologies, written in the C programming language.

Certificate Authority Software
------------------------------

`Krill <https://nlnetlabs.nl/projects/rpki/krill/>`_
   RPKI Certificate Authority software by NLnet Labs, written in the Rust 
   programming language.

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

`RTRLib <https://github.com/rtrlib/rtrlib>`_
   The RTRlib implements the client-side of the RPKI-RTR protocol (RFC
   6810, RFC 8210) and BGP Prefix Origin Validation (RFC 6811). This also
   enables the maintenance of router keys, which are required to
   deploy BGPSec.
   
   RTRlib was originally founded by researchers from the Computer Systems & Telematics
   group at Freie Universität Berlin and reseachers from the INET research group at
   Hamburg University of Applied Sciences, under the supervision of Matthias Wählisch
   and Thomas Schmidt. It is now a community project.

Insights and Statistics
-----------------------

There are several initiatives that measure the adoption and data quality of RPKI:

- `Global certificate and ROA statistics <http://certification-stats.ripe.net>`_, by RIPE NCC
- `Global country statistics <https://lirportal.ripe.net/certification/content/static/statistics/world-roas.html>`_, by RIPE NCC
- `Cirrus Certificate Transparency Log <https://ct.cloudflare.com/logs/cirrus>`_, by Cloudflare
- `The RPKI Observatory <https://nusenu.github.io/RPKI-Observatory/>`_, by nusenu
- `RPKI Deployment Monitor <https://rpki-monitor.antd.nist.gov>`_, by NIST