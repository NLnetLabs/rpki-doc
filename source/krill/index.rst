.. _doc_krill:

Krill
=====

Krill is a free, open source Resource Public Key Infrastructure (RPKI) daemon,
featuring a Certificate Authority (CA) and publication server, written by `NLnet
Labs <https://nlnetlabs.nl>`_.

You are welcome to ask questions or post comments and ideas on our `RPKI mailing
list <https://nlnetlabs.nl/mailman/listinfo/rpki>`_. If you find a bug in Krill,
feel free to `create an issue <https://github.com/NLnetLabs/krill/issues>`_ on
GitHub. Krill is distributed under the Mozilla Public License 2.0.

.. figure:: img/krill-ui-welome.png
    :align: center
    :width: 100%
    :alt: Welcome to Krill

------------

**Krill is intended for:**

- **Organisations who hold address space from multiple Regional Internet
  Registries (RIRs). Using Krill, ROAs can be managed seamlessly for all
  resources within one system.**
- **Organisations that need to be able to delegate RPKI to their customers or
  different business units, so that that they can run their own CA and manage
  ROAs themselves.**
- **Organisations who do not wish to rely on the web interface of the hosted
  systems that the RIRs offer, but require RPKI management that is integrated
  with their own systems using a common UI or API.**

------------

Using Krill, you can run your own RPKI Certificate Authority as a child of one
or more parent CAs, usually a Regional Internet Registry (RIR) or National
Internet Registry (NIR). With Krill you can run under multiple parent CAs
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

------------

You can choose to run Krill as a standalone application or run it together with
:ref:`doc_krill_manager`, a tool that brings together all of the puzzle pieces
needed to administer and run Delegated RPKI with Krill as a highly available
scalable service.

Krill Manager includes Docker, Gluster, NGINX, Rsyncd, as well as Prometheus and
Fluentd outputs for monitoring and log analysis. The integrated setup wizard
allows for seamless TLS configuration, optionally using Let's Encrypt, as well
as automated updating of the application itself and all included components.

Krill with Krill Manager is available for free as a 1-Click App on the `AWS
Marketplace <https://aws.amazon.com/marketplace/pp/B0886F8GNJ>`_ and the
`DigitalOcean Marketplace
<https://marketplace.digitalocean.com/apps/krill?refcode=cab39584666c>`_.

.. toctree::
   :maxdepth: 2
   :name: toc-krill

   before-you-start
   install-and-run
   get-started
   parent-interactions
   creating-a-certificate-authority
   registering-with-a-parent
   remote-publishing
   cli
   api
   monitoring
   publication-server
   testing
   docker
   Running with Krill Manager <krillmanager/index>
.. history
.. authors
.. license
