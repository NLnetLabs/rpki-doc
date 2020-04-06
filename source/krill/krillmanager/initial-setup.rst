.. _doc_krill_manager_initial_setup:

Initial Setup
=============

Run the ``krillmanager init`` command to launch the interactive setup wizard.

Exiting the wizard
------------------

Press ``CTRL-C`` at any time to abort the wizard. You can use the
``krillmanager init`` command later to run the wizard again.

Automatic upgrade
-----------------

On first launch Krill Manager will automatically upgrade itself to a newer
version if available:

.. code-block:: bash

  # krillmanager init
  A new version is available (v0.1.0 => v0.1.3).
  Automatically upgrading to newer version v0.1.3..
  Checking for newer version
  Fetching newer version (v0.1.3)
  Running post-upgrade actions
  Upgrading host files
  Upgrading dependencies
  [###############-------------------------] 38% Upgrading dependencies

.. note:: This is an upgrade of Krill **Manager**, which *might not* include an
          upgrade of Krill, it may just be an upgrade to the Manager itself
          and/or to one or more of the other components such as NGINX or Rsync.
          The version number shown is the version number of Krill Manager, not
          of Krill.

Manual upgrade
--------------

If you have previously completed the wizard and later run ``krillmanager init``
again, if a new version is available you will be offered the choice to upgrade:

.. code-block:: bash

  # krillmanager init
  A new version is available (v0.1.0 => v0.1.3).

  > Would you like to upgrade? [YES/NO]: YES

The default action is to upgrade, but you can continue without upgrading.

Step by step help
-----------------

Once any available upgrade is complete you will be presented with the welcome
page of the wizard. Below you will find help for each of the possible wizard
pages to help you along the way:

.. toctree::
   :maxdepth: 1
   :name: toc-krill-manager-wizard

   wizard/welcome
   wizard/restore-from-backup
   wizard/publication-mode
   wizard/ca-name
   wizard/domains
   wizard/authentication
   wizard/logging

.. history
.. authors
.. license

