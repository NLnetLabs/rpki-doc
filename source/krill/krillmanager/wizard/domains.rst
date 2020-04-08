.. _doc_krill_manager_wizard_domains:

Wizard: Domains
===============

Krill Manager needs to know which domain names your clients will be expected to
use in order to contact your Krill services. The domains that you need to
specify in this step are influenced by the choice you made in the
:ref:`doc_krill_manager_wizard_publication_mode` step:

- **3rd Party Mode**: only a single domain name for the Krill UI and API is required.

  .. code-block:: text

    KRILL SETUP WIZARD: Domains                            [next: Authentication]
    -----------------------------------------------------------------------------

    Which domain name(s) will Krill, RRDP and Rsync on this Droplet be reachable
    at?

    Warning: These domain names may be the same, or different. You should ensure
    that they are registered in DNS and resolve to this Droplet.

    > Krill domain:


- **Self-Publishing Mode**: additional domains for RRDP and Rsync will be requested.

  .. code-block:: text

    KRILL SETUP WIZARD: Domains                            [next: Authentication]
    -----------------------------------------------------------------------------

    Which domain name(s) will Krill, RRDP and Rsync on this Droplet be reachable
    at?

    Warning: These domain names may be the same, or different. You should ensure
    that they are registered in DNS and resolve to this Droplet.

    > Krill domain: ca.demo.krill.cloud
    > RRDP domain: rrdp.demo.krill.cloud
    > Rsync domain: rsync.demo.krill.cloud

.. Warning:: The domain names that you enter in this page of the wizard should
             already be configured to point at your Krill Manager IP address.

.. Note:: Later in the process the wizard will offer to obtain Let's Encrypt
          certificates on your behalf for the Krill and RRDP domains that you
          supply on this page of the wizard.

Domain Validity
---------------

Krill Manager will attempt to lookup the DNS records for the given domain names
in order to verify that they are valid. If not found, Krill Manager will warn
you.

If you are **sure** that the domain name is correct but DNS propagation has not
completed yet, or for some other reason you would like to proceed, Krill Manager
allows you to ignore the lookup failure:

.. code-block:: text

  KRILL SETUP WIZARD: Domains                            [next: Authentication]
  -----------------------------------------------------------------------------

  Which domain name(s) will Krill, RRDP and Rsync on this Droplet be reachable
  at?

  Warning: These domain names may be the same, or different. You should ensure
  that they are registered in DNS and resolve to this Droplet.

  > Krill domain: foo.bar
  DNS lookup for this domain did not return any results

  > Are you sure you wish to use this domain? [YES/NO]:
