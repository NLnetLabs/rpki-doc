.. _doc_krill_manager:

Krill Manager
=============

.. Note:: The basic usage of Krill Manager has been documented but more advanced
          topics are still being written. To stay informed please subscribe to
          the `NLnet Labs RPKI mailing list <https://lists.nlnetlabs.nl/mailman/listinfo/rpki>`_.

Krill Manager is a tool for running Krill as a highly available scalable service
with integration points for monitoring and log analysis.

Krill Manager is available as a `DigitalOcean Marketplace 1-Click App
<https://marketplace.digitalocean.com/apps/krill>`_. You can watch an
introduction to its capabilities in the video below. It walks through setting up
Krill and all additional components using the 1-Click App, configure the
Certificate Authority to run Delegated RPKI under a Regional Internet Registry
and create ROAs, all in just 6 minutes real-time.

.. raw:: html

    <div style="position: relative; width: 100%; height: 0;
    padding-bottom: 56.25%; margin-bottom: 20px;">
        <iframe style="position: absolute; top: 0; left: 0; width: 100%; height:
        100%;" src="https://www.youtube.com/embed/qunvH2t6rqU" frameborder="0"
        allow="accelerometer; autoplay; encrypted-media; gyroscope;
        picture-in-picture" allowfullscreen></iframe>
    </div>

In future we intend to also publish instructions for using Krill Manager in
other hosting environments. For the adventurous you should be able to run Krill
Manager on any Linux host that has Bash, Docker and Gluster installed.

.. toctree::
   :maxdepth: 1
   :name: toc-krill-manager

   prerequisites
   initial-setup
   using-the-cli
   monitoring
   logging
   security
   scaling-out
.. history
.. authors
.. license
