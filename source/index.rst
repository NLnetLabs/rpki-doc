.. The RPKI Documentation master file, created by
   sphinx-quickstart on Tue Jan  8 13:38:23 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

RPKI Documentation
==================

|lastupdated| |discord|

.. |lastupdated| image:: https://img.shields.io/github/last-commit/NLnetLabs/rpki-doc.svg?label=last%20updated&style=flat-square

.. |discord| image:: https://img.shields.io/discord/818584154278199396?label=rpki%20on%20discord&logo=discord&style=flat-square
            :target: https://discord.gg/8dvKB5Ykhy

Welcome to the documentation of the Resource Public Key Infrastructure (RPKI),
the community-driven technology based on open standards that is aimed at making
Internet routing more secure. If you are new to this documentation, we recommend
that you read the :ref:`introduction page <doc_about_intro>` to get an overview
of what this documentation has to offer.

.. note:: This documentation is an open source project maintained by the RPKI
          team at NLnet Labs, with contributions from  the network operator
          community around the world. We always appreciate your feedback and
          improvements.

          You can submit an issue or pull request on the `GitHub repository
          <https://github.com/NLnetLabs/rpki-doc/issues>`_, post a message on
          the `RPKI mailing list
          <https://lists.nlnetlabs.nl/mailman/listinfo/rpki>`_ or discuss RPKI
          on `Discord <https://discord.gg/WaPgs8vEKy>`_. 

The main documentation is organised into the following sections:

.. toctree::
   :maxdepth: 2
   :caption: General
   :name: sec-general

   about/introduction
   about/faq
   about/help

.. toctree::
   :maxdepth: 2
   :caption: RPKI Technology
   :name: sec-rpki-tech

   rpki/introduction
   rpki/bgp-routing
   rpki/securing-bgp
   rpki/implementation-models
   rpki/using-rpki-data


.. toctree::
   :maxdepth: 2
   :caption: Operations
   :name: sec-rpki-ops
   
   ops/router-support
   ops/resources
   ops/tools

.. toctree::
   :maxdepth: 2
   :caption: RPKI Tools
   :name: sec-rpki-tools

   krill/index


Index
=====

* :ref:`genindex`
