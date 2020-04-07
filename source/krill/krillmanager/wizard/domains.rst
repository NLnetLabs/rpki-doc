.. _doc_krill_manager_wizard_domains:

Wizard: Domains
====================

Krill Manager needs to know which domain names your clients will be expected to
use in order to contact your Krill services. The domains that you need to
specify in this step are influenced by the choice you made in the
:ref:`doc_krill_manager_wizard_publication_mode` step:

- **3rd Party Mode**: only a single domain name for the Krill UI and API is required.

  .. figure:: img/domains-3rd-party.png
     :alt: Wizard intiial domains page screenshot when using 3rd party mode.

     A Krill Manager instance in 3rd-party mode.


- **Self-Publishing Mode**: additional domains for RRDP and Rsync will be requested.

  .. figure:: img/domains-self-publish.png
     :alt: Wizard intiial domains page screenshot when using self-publishing mode.

     A Krill Manager instance in self-publish mode.

.. Caution:: The domain names that you enter in this page of the wizard should
          already be configured to point at your Krill Manager IP address.

.. Note:: Later in the process the wizard will offer to obtain Let's Encrypt
         certificates on your behalf for the Krill and RRDP domains that you
         supply on this page of the wizard.

Domain Validity
---------------

Krill Manager will attempt to lookup the DNS records for the given domain names
in order to verify that they are valid. If not found, Krill Manager will warn you.

If you are **sure** that the domain name is correct but DNS propagation has not
completed yet, or for some other reason you would like to proceed, Krill
Manager allows you to ignore the lookup failure:

.. figure:: img/domains-invalid.png
   :alt: Wizard intiial domains page screenshot with an invalid domain.

   Krill Manager warning about an unresolvable domain name.