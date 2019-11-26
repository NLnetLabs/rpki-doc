.. The RPKI Documentation master file, created by
   sphinx-quickstart on Tue Jan  8 13:38:23 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

RPKI Documentation
==================

.. image:: https://img.shields.io/github/last-commit/NLnetLabs/rpki-doc.svg?label=last%20updated&style=flat-square

Welcome to the documentation of the Resource Public Key Infrastructure (RPKI), the community-driven technology based on open standards that is aimed at making Internet routing more secure. If you are new to this documentation, we recommend that you read the :ref:`introduction page <doc_about_intro>` to get an overview of what this documentation has to offer.

The table of contents below and in the sidebar should let you easily access the documentation for your topic of interest. You can also use the search function in the top left corner.

.. note:: This documentation is an open source project maintained by the RPKI team at
          NLnet Labs, with contributions from  the network operator community around
          the world. We always appreciate your feedback and improvements.

          You can submit an issue or pull request on the `GitHub repository
          <https://github.com/NLnetLabs/rpkidoc/issues>`_, or post a message on the
          `RPKI mailing list <https://nlnetlabs.nl/mailman/listinfo/rpki>`_. If you
          are interested in providing a translation for this project, please
          read `this guide
          <https://docs.readthedocs.io/en/latest/guides/manage-translations.html>`_
          to get started.

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
   rpki/router-support
   rpki/resources

.. toctree::
   :maxdepth: 2
   :caption: RPKI Tools
   :name: sec-rpki-tools

   tools
   krill/index
   routinator/index
   rtrlib/index

.. Indices and tables
.. ------------------
..
.. * :ref:`genindex`
.. * :ref:`modindex`
.. * :ref:`search`
