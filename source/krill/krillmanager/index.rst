.. _doc_krill_manager:

Krill Manager
=============

Krill Manager is a tool for running Krill as a highly available scalable
service. It brings together all of the puzzle pieces needed to administer and
run Delegated RPKI with Krill.

Krill Manager includes Docker, Gluster, NGINX, Rsyncd, as well as Prometheus and
Fluentd outputs for monitoring and log analysis. The integrated setup wizard
allows for seamless TLS configuration, optionally using Let's Encrypt, as well
as automated updating of the application itself and all included components.

Krill with Krill Manager is available for free as a 1-Click App on the `AWS
Marketplace <https://aws.amazon.com/marketplace/pp/B0886F8GNJ>`_ and the
`DigitalOcean Marketplace
<https://marketplace.digitalocean.com/apps/krill?refcode=cab39584666c>`_.
Hosting Krill Manger yourself is possible too, you should be able to run it on
any Linux host that has Bash, Docker and Gluster installed.

You can watch an introduction to the capabilities of Krill Manager in the video
below. It walks through setting up Krill and all additional components using the
1-Click App, configure the Certificate Authority to run Delegated RPKI under a
Regional Internet Registry and create ROAs, all in just 6 minutes real-time.

.. raw:: html

    <div style="position: relative; width: 100%; height: 0;
    padding-bottom: 56.25%; margin-bottom: 20px;">
        <iframe style="position: absolute; top: 0; left: 0; width: 100%; height:
        100%;" src="https://www.youtube.com/embed/qunvH2t6rqU" frameborder="0"
        allow="accelerometer; autoplay; encrypted-media; gyroscope;
        picture-in-picture" allowfullscreen></iframe>
    </div>

.. toctree::
   :maxdepth: 1
   :name: toc-krill-manager

   updates
   prerequisites
   initial-setup
   using-the-cli
   monitoring
   logging
   security
   cluster-mode
.. history
.. authors
.. license
