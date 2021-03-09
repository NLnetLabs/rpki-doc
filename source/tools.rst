.. index:: ! Software Projects

.. _doc_tools:

Software Projects
=================

This section provides an overview of all well known open source projects that
support RPKI. It includes Relying Party software for validating RPKI data,
Certificate Authority software to run RPKI on your own infrastructure and
supporting tools that help deployment and integration.

.. index:: Relying Party software
  see: RPKI Validator; Relying Party software

.. _relying_party_software:

Relying Party Software
----------------------

.. csv-table:: 
   :header: "Name", "Maintainer", "Language", "Last Commit" 

   "`FORT Validator <https://github.com/NICMx/FORT-validator>`_", "NIC.mx", "C", ".. image:: https://img.shields.io/github/last-commit/NICMx/FORT-validator?label=%20&style=flat-square"
   "`OctoRPKI <https://github.com/cloudflare/cfrpki#octorpki>`_", "Cloudflare", "Go", ".. image:: https://img.shields.io/github/last-commit/cloudflare/cfrpki?label=%20&style=flat-square"
   "`rcynic <https://github.com/dragonresearch/rpki.net>`_", "Dragon Research Labs", "Python", ".. image:: https://img.shields.io/github/last-commit/dragonresearch/rpki.net?label=%20&style=flat-square"   
   "`Routinator <https://github.com/NLnetLabs/routinator>`_", "NLnet Labs", "Rust", ".. image:: https://img.shields.io/github/last-commit/nlnetlabs/routinator?label=%20&style=flat-square"
   "`rpki-client <https://github.com/rpki-client/rpki-client-portable>`_", "OpenBSD", "C", ".. image:: https://img.shields.io/github/last-commit/rpki-client/rpki-client-portable?label=%20&style=flat-square"
   "`rpki-prover <https://github.com/lolepezy/rpki-prover>`_", "Misha Puzanov", "Haskell", ".. image:: https://img.shields.io/github/last-commit/lolepezy/rpki-prover?label=%20&style=flat-square"
   "`RPKI Validator <https://github.com/RIPE-NCC/rpki-validator-3>`_", "RIPE NCC", "Java", ".. image:: https://img.shields.io/github/last-commit/RIPE-NCC/rpki-validator-3?label=%20&style=flat-square"
   "`RPSTIR2 <https://github.com/bgpsecurity/rpstir2>`_", "ZDNS", "Go", ".. image:: https://img.shields.io/github/last-commit/bgpsecurity/rpstir2?label=%20&style=flat-square"

.. index:: Certificate Authority software

Certificate Authority Software
------------------------------

.. csv-table:: 
   :header: "Name", "Maintainer", "Language", Last Commit 

   "`Krill <https://github.com/NLnetLabs/krill>`_", "NLnet Labs", "Rust", ".. image:: https://img.shields.io/github/last-commit/NLnetLabs/krill?label=%20&style=flat-square"
   "`rpkid <https://github.com/dragonresearch/rpki.net>`_", "Dragon Research Labs", "Python", ".. image:: https://img.shields.io/github/last-commit/dragonresearch/rpki.net?label=%20&style=flat-square"

Supporting Tools
----------------

`BGP-SRx <https://www.nist.gov/services-resources/software/bgp-secure-routing-extension-bgp-srx-prototype>`_
   SRx is an open source reference implementation and research platform by the
   National Institute for Standards and Technology (NIST). It is intended for
   investigating emerging BGP security extensions and supporting protocols such
   as RPKI Origin Validation and BGPSec Path Validation.

`GoRTR <https://github.com/cloudflare/gortr>`_
   An open-source implementation of RPKI to Router protocol
   (:RFC:`6810`) using the Go programming language. This project is
   maintained by Cloudflare.

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
   Internet would look like after deploying a *"invalid == reject"* policy.

`rpki-ov-checker <https://github.com/job/rpki-ov-checker>`_
   rpki-ov-checker is an open source utility to quickly analyse BGP RIB dumps
   and the potential impact of deploying "invalid is reject" routing policies.

:ref:`rtrlib`
   The RTRlib implements the client-side of the RPKI-RTR protocol
   (:RFC:`6810`, :RFC:`8210`) and BGP Prefix Origin
   Validation (:RFC:`6811`). This also enables the maintenance of
   router keys, which are required to deploy BGPSec.

`RTRTR <https://github.com/NLnetLabs/rtrtr>`__
   An RPKI data proxy maintained by NLnet Labs, allowing operators to centralise
   validation and distribute the validated data to various points of presence
   via the RTR protocol, or JSON over HTTPS.