Krill
=====

Krill is a free, open source Resource Public Key Infrastructure (RPKI) daemon,
featuring a Certificate Authority (CA) and publication server, written by NLnet
Labs in the Rust programming language.

Using Krill, you can run your own RPKI Certificate Authority as a child of one
or more parent CAs, usually a Regional Internet Registry (RIR) or National
Internet Registry (NIR). With Krill you can run multiple parent CAs
seamlessly and transparently. This is especially convenient if your organisation
holds address space in several RIR regions, as it can all be managed as a
single pool.

Krill can also act as a parent for child CAs. This means you can delegate
resources down to children of your own, e.g. business units, departments,
members or clients, who, in turn, manage ROAs themselves.

Lastly, Krill features a separate publication server so you can either
publish your certificate and ROAs with a third party, such as your NIR or RIR,
or you publish them yourself. In the latter case, you will need to run Krill
alongside a publicly available Rsyncd and HTTPS server.

Summarising, Krill is intended for:

  - Organisations who do not wish to rely on the web interface of the hosted
    systems that the RIRs offer, but require RPKI management that is integrated
    with their own systems
  - Organisations that need to be able to delegate RPKI to their customers or
    different business units, so that that they can run their own CA and manage
    ROAs themselves
  - Organisations that manage address space from multiple RIRs. Using Krill,
    they can manage all ROAs for all resources seamlessly within one system
  - Organisations who want to be operationally independent from their parent
    RIR, such as NIRs or Enterprises

Krill currently can be managed from the command line, through an `API
<http://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/NLnetLabs/krill/v0.4.2/doc/openapi.yaml>`_
and with a user interface. The UI for Krill is available as a separate, optional
project named `Lagosta <https://github.com/NLnetLabs/lagosta>`_.

If you want to know more about the development of Krill, please have a look at
the `project plan
<https://github.com/NLnetLabs/krill/projects?query=is%3Aopen+sort%3Aname-asc/>`_
on GitHub. If you have any questions, comments or ideas, you are welcome  to
discuss them on our `RPKI mailing list
<https://nlnetlabs.nl/mailman/listinfo/rpki>`_, or feel free to `create an issue
<https://github.com/NLnetLabs/krill/issues>`_ on GitHub.

.. toctree::
   :maxdepth: 2
   :name: toc-krill

   installation
   get-started
   running
   running-docker
   using-cli
   using-api
   manage-cas
   remote-publishing
   testing
.. history
.. authors
.. license
