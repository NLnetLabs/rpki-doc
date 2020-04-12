.. _doc_krill_manager:

.. Warning:: These pages are still being written. To stay informed please
             subscribe to the `NLnet Labs RPKI mailing list <https://lists.nlnetlabs.nl/mailman/listinfo/rpki>`_.

Krill Manager
=============

Krill Manager is a tool for running Krill as a highly available scalable service with integration points for monitoring and log analysis.

.. Tip:: Krill Manager is available as a `DigitalOcean Marketplace 1-Click App <https://marketplace.digitalocean.com/apps/krill>`_.
         In future we hope to also publish instructions for using
         Krill Manager in other hosting environments. For the adventurous you
         should be able to run Krill Manager on any Linux host that has Bash,
         Docker and Gluster installed.

You can watch an introduction to the capabilities of Krill Manager in this
video. It walks through setting up Krill and all additional components using the
1-Click App on the DigitalOcean Marketplace, configure the Certificate Authority
to run Delegated RPKI under a Regional Internet Registry and create ROAs, all in
just 6 minutes real-time.

.. raw:: html

    <div style="position: relative; padding-bottom: 4%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe width="697" height="392" src="https://www.youtube.com/embed/qunvH2t6rqU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

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
