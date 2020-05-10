.. _doc_krill_manager_prerequisites:

Prerequisites
=============

Required
--------

To use Krill Manager you will need the following:

- A DigitalOcean_ or AWS_ account *(to create the virtual machine that will run Krill Manager,
  Krill, et al)*.
- The ability to create DNS subdomain A records *(to point one or more domains
  at the new VM)*.

.. _DigitalOcean: https://m.do.co/c/cab39584666c
.. _AWS: https://aws.amazon.com/

Optional
--------

- Your own TLS certificate and key file(s) (in PEM format) for the domain(s)
  that you wish to use with Krill. When using your own TLS certificate files
  you will need to upload them to the VM *before* performing the
  :ref:`initial setup <doc_krill_manager_initial_setup>`, e.g.:

  .. code-block:: bash

     scp /local/path/to/certificate.pem username@<IP address>:/tmp/

  .. Tip:: It is not necessary to use your own TLS certificates as Krill Manager
           can obtain for you a Let's Encrypt TLS certificate per configured
           domain. Krill Manager will ensure that Let's Encrypt certificates are
           renewed before they expire.

- Connection details and credentials for an AWS S3 compatible service to which
  host and application logs can be uploaded periodically.
