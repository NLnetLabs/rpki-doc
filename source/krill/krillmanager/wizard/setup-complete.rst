.. _doc_krill_manager_wizard_setup_complete:

Wizard: Setup Complete
======================

Once everything is setup the wizard will report the status of the running
services and the locations at which the services can be found:

.. code-block:: text

  KRILL SETUP WIZARD: Setup complete                                [next: END]
  -----------------------------------------------------------------------------

  Service status summary:
  cert_renewer   1/1
  host_metrics   1/1
  krill          1/1
  nginx          1/1
  nginx_metrics  1/1
  rsyncd         1/1
  All services appear to be running.

  Krill and related services should now be available as follows:
    - Krill Web Portal: https://ca.demo.krill.cloud/ (token: xxxxxxxxxxxx)
    - RRDP URI        : https://rrdp.demo.krill.cloud/rrdp/
    - Rsync URI       : rsync://rsync.demo.krill.cloud/repo/
    - Prometheus monitoring endponts:
      - Krill         : http://ca.demo.krill.cloud:9657/metrics
      - NGINX         : http://ca.demo.krill.cloud:9113/metrics
      - Docker        : http://ca.demo.krill.cloud:9323/metrics
      - O/S           : http://ca.demo.krill.cloud:9100/metrics
      ...

  Please consult the documentation for guidance on administering and
  monitoring these services.

  Thanks,

  The NLnet Labs RPKI team.

  Press any key to continue:

Verify that Krill is Running
----------------------------

Use the ``Krill Web Portal`` link and token to login to the Krill UI where
you should see your newly created Certificate Authority and the details
required to link your CA to a parent:

.. figure:: img/krill-ui.png
    :alt: Krill UI screenshot.

Refer to the :doc:`Krill Documentation </krill/index>` to learn more about
Krill.

Next Steps
----------

Click Next or return :doc:`to the index </krill/krillmanager/index>` to
continue learning about Krill Manager.
