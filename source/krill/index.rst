Krill
=====

Krill is a free, open source Resource Public Key Infrastructure (RPKI) daemon,
featuring a Certificate Authority (CA) and publication server, written by `NLnet
Labs <https://nlnetlabs.nl>`_ in the `Rust programming language
<https://www.rust-lang.org>`_.

Using Krill, you can run your own RPKI Certificate Authority as a child of one
or more parent CAs, usually a Regional Internet Registry (RIR) or National
Internet Registry (NIR). With Krill you can run multiple parent CAs
seamlessly and transparently. This is especially convenient if your organisation
holds address space in several RIR regions, as it can all be managed as a
single pool.

Krill can also act as a parent for child CAs. This means you can delegate
resources down to children of your own, such as business units, departments,
members or customers, who, in turn, manage ROAs themselves.

Lastly, Krill features a publication server so you can either publish your
certificate and ROAs with a third party, such as your NIR or RIR, or you publish
them yourself. Krill can be managed with a web user interface, from the command
line and through an API.

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

Krill offers all features needed to be used in production environments. The
toolset is actively being developed with new releases every couple of weeks.
If you want to know more about the development of Krill, please have a look at
the `project plan
<https://github.com/NLnetLabs/krill/projects?query=is%3Aopen+sort%3Aname-asc/>`_
on GitHub. If you have any questions, comments or ideas, you are welcome  to
discuss them on our `RPKI mailing list
<https://nlnetlabs.nl/mailman/listinfo/rpki>`_, or feel free to `create an issue
<https://github.com/NLnetLabs/krill/issues>`_ on GitHub. Krill is distributed
under the Mozilla Public License 2.0.

.. toctree::
   :maxdepth: 2
   :name: toc-krill

   installation
   getting-started
   parent-interactions
   publication-server
   remote-publishing
   ui
   cli
   api
   docker
   monitoring
   testing
.. history
.. authors
.. license
