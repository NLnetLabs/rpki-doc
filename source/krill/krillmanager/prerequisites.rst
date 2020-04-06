.. _doc_krill_manager_prerequisites:

Prerequisites
=============

Required
--------

To use Krill Manager you will need the following:

- A DigitalOcean_ account *(to create the Droplet that will run Krill Manager,
  Krill, et al)*.
- The ability to create DNS subdomain A records *(to point one or more domains
  at the new Droplet)*.

.. _DigitalOcean: https://www.digitalocean.com/

Optional
--------

- Your own TLS certificate and key file(s) (in PEM format) for the domain(s)
  that you wish to use with Krill. When using your own TLS certificate files
  you will need to upload them to the Droplet *before* performing the 
  :ref:`initial setup <doc_krill_manager_initial_setup>`, e.g.:

             .. code-block:: bash

               scp /local/path/to/certificate.pem root@<droplet IP address>:/tmp/

.. Tip:: It is not necessary to use your own TLS certificates as Krill Manager
         can obtain for you a Let's Encrypt TLS certificate per configured
         domain. Krill Manager will ensure that Let's Encrypt certificates are
         renewed before they expire.
