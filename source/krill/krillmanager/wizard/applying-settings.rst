.. _doc_krill_manager_wizard_applying_settings:

Wizard: Applying Settings
=========================

At this stage the wizard has everything it needs to generate application
configuration files based on the settings chosen in the earlier wizard pages
and to launch the applications:

.. code-block:: text

  KRILL SETUP WIZARD: Applying settings                  [next: Setup complete]
  -----------------------------------------------------------------------------

  Generating Krill configuration file
  Preparing NGINX configuration
  Preparing RSYNCD configuration
  Creating network krill_default
  Creating service krill_cert_renewer
  Creating service krill_host_metrics
  Creating service krill_krill
  Creating service krill_nginx
  Creating service krill_nginx_metrics
  Creating service krill_rsyncd
  Waiting for services to become ready..
  [###################################-----] 88% Starting services..

Once the applications are running the wizard will configure the CA Name you
requested (assuming no CA exists already), and in self-publishing mode the
embedded Krill repository will be configured for use by the newly created
CA:

.. code-block:: text

  Waiting for services to become ready..

  Creating CA 'Acme-Corp-Intl'..
  Registering CA 'Acme-Corp-Intl' with the embedded repository..
