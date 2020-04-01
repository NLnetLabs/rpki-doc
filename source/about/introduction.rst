.. index:: ! Introduction

.. _doc_about_intro:

Introduction
============

Welcome to the documentation of the Resource Public Key Infrastructure (RPKI).
Here, we aim to offer an overview of the RPKI technology itself, as well as some
of the tools that are being developed for it. Any software implementer is welcome
to add documentation for their tools to this project.

This page gives a broad overview of the RPKI and how it can help make Internet
routing using the Border Gateway Protocol (BGP) more secure. This way, you will
learn how RPKI can benefit your organisation, as well as helping others to be
more secure on the Internet.

About this Documentation
------------------------

This documentation is continuously written, corrected and edited by the RPKI
team at NLnet Labs. An initial version was written by Alex Band, Tim Bruijnzeels
and Martin Hoffmann. Over time, additions from the network operator community,
researchers and interested parties around the world were contributed. The
documentation is edited via text files in the `reStructuredText
<http://www.sphinx-doc.org/en/stable/rest.html>`_ markup language and then
compiled into a static website/offline document using the open source `Sphinx
<http://www.sphinx-doc.org>`_  and `ReadTheDocs <https://readthedocs.org/>`_
tools.

.. note:: You can contribute to the RPKI documentation by opening an issue
          or sending patches via pull requests on the `GitHub
          source repository <https://github.com/NLnetLabs/rpki-doc>`_.

All the contents are under the permissive Creative Commons Attribution 3.0
(`CC-BY 3.0 <https://creativecommons.org/licenses/by/3.0/>`_) license, with
attribution to "The RPKI team at NLnet Labs and the RPKI community".

About Resource Public Key Infrastructure
----------------------------------------

RPKI allows holders of Internet number resources to make verifiable statements
about how they intend to use their resources. To achieve this, it uses a public
key infrastructure that creates a chain of resource certificates that follows
the same structure as the way IP addresses and AS numbers are handed down.

RPKI is used to make Internet routing more secure. It is a community-driven
system in which open source software developers, router vendors and all five
Regional Internet Registries (RIRs) participate, i.e. `ARIN
<https://www.arin.net/resources/rpki/>`_, `APNIC
<https://www.apnic.net/community/security/resource-certification/>`_, `AFRINIC
<https://www.afrinic.net/resource-certification>`_, `LACNIC
<https://www.lacnic.net/640/2/lacnic/general-information-resource-certification-system-rpki>`_
and `RIPE NCC
<https://www.ripe.net/manage-ips-and-asns/resource-management/certification/>`_.

Currently, RPKI is used to let the legitimate holder of a block of IP addresses
make an authoritative statement about which AS is authorised to originate their
prefix in the BGP. In turn, other network operators can download and validate
these statements and make routing decisions based on them. This process is
referred to as route origin validation (ROV). This provides a stepping stone to
provide path validation in the future.

Organisation of this Documentation
----------------------------------

This documentation is organised into three main sections:

- The :ref:`sec-general` section contains this introduction as well as
  information about the licensing, authors, etc. It also contains the
  :ref:`doc_faq` and the :ref:`doc_quick_help`.
- The :ref:`sec-rpki-tech` section explains the RPKI technology and standards in
  order for you to get a good sense of the requirements and moving parts. It
  will help you choose the right RPKI solution for your organisation, with
  regards to generating, publishing and using RPKI data.
- The :ref:`sec-rpki-tools` section is about various open source projects that
  are maintained to support RPKI.
