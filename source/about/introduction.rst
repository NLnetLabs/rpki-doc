.. _doc_about_intro:

Introduction
------------

Welcome to the documentation of the Resource Public Key Infrastructure (RPKI). Here, we aim to offer an overview of the RPKI technology itself, as well as the tools that NLnet Labs is developing for it. 

This page gives a broad overview of the RPKI and how it can help make Internet routing using the Border Gateway Protocol (BGP) more secure. This way, you will learn how RPKI can benefit your organisation, as well as helping others to be more secure on the Internet.

About this documentation
++++++++++++++++++++++++

This documentation is continuously written, corrected and edited. An initial version was written by Alex Band, Tim Bruijnzeels, Martin Hoffmann, Job Snijders, David Monosov and Melchior Aelmans. Over time, many additions from the network operators community, researchers and interested parties around the world were contributed. It is edited via text files in the
`reStructuredText <http://www.sphinx-doc.org/en/stable/rest.html>`_ markup
language and then compiled into a static website/offline document using the
open source `Sphinx <http://www.sphinx-doc.org>`_ tool.

.. note:: You can contribute to the RPKI documentation by opening issue tickets
          or sending patches via pull requests on its GitHub
          `source repository <https://github.com/NLnetLabs/rpki-doc>`_.

All the contents are under the permissive Creative Commons Attribution 3.0
(`CC-BY 3.0 <https://creativecommons.org/licenses/by/3.0/>`_) license.

About RPKI
++++++++++

The global routing system of the Internet consists of a number of functionally independent actors (Autonomous Systems) which use BGP (Border Gateway Protocol) to exchange routing information. The system is very dynamic and flexible by design. Connectivity and routing topologies are subject to change. Changes easily propagate globally within a few minutes. One weakness of this system is that these changes cannot be validated against information existing outside of the BGP protocol itself.

Resource Public Key Infrastructure (RPKI) is a way to define data in an out-of-band system such that the information that is exchanged by BGP can be validated to be correct. The RPKI standards were developed by the IETF (Internet Engineering Task Force) to describe some of the resources of the Internet’s routing and addressing scheme in a cryptographic system.

RPKI is a community-driven system in which open source software developers, router vendors and all five Regional Internet Registries (RIRs) participate, i.e. `ARIN <https://www.arin.net/resources/rpki/>`_, `APNIC <https://www.apnic.net/community/security/resource-certification/>`_, `AFRINIC <https://www.afrinic.net/resource-certification>`_, `LACNIC <https://www.lacnic.net/640/2/lacnic/general-information-resource-certification-system-rpki>`_ and `RIPE NCC <https://www.ripe.net/manage-ips-and-asns/resource-management/certification/>`_. Using the RPKI system, the legitimate holder of a block of IP addresses can make an authoritative statement about which Autonomous System (AS) is authorised to originate their prefix in the BGP. In turn, other network operators can download and validate these statements and make routing decisions based on them. This process is referred to as Route Origin Validation (ROV).

Organisation of the documentation
+++++++++++++++++++++++++++++++++

This documentation is organised in four main sections:

- The :ref:`sec-general` section contains this introduction as well as
  information about the licensing, authors, etc. It also contains the :ref:`doc_faq`.
- The :ref:`sec-rpki` section explains the RPKI technology and standards in order for you to get a good sense of the requirements and moving parts. It will help you choose the right RPKI solution for your organisation, with regards to generating, publishing and using RPKI data.
- The section :ref:`sec-creating-rpki-data` is about Krill, the RPKI daemon that acts a Certificate Authority and Publication Server. If your organisation wants to run a self-hosted RPKI solution, this is what you should read.
- The :ref:`sec-using-rpki-data` section covers the various Relying Party Software packages — also known as Validators — that can be used to download the global RPKI data set, validate it and use the result in your BGP decision making process.
