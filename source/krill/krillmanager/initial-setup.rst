.. _doc_krill_manager_initial_setup:

Initial Setup
=============

First, log into the virtual machine you created using SSH. Note that for AWS
Marketplace EC2 instances you have use the username ``ubuntu`` and for
DigitalOcean Marketplace droplets the username ``root``.

Next, run the ``krillmanager init`` command to launch the interactive setup
wizard.

.. Important:: The wizard covers the most common cases. It does **NOT** yet
               support clustered or advanced log streaming setups or overriding
               the Krill or NGINX or RsyncD configuration. Such setups are
               possible but not yet via the wizard.

Exiting the Wizard
------------------

Press ``CTRL-C`` at any time to abort the wizard. You can use the
``krillmanager init`` command later to run the wizard again.

Automatic Upgrade
-----------------

On first launch Krill Manager will automatically upgrade itself to a newer
version if available:

.. code-block:: text

  # krillmanager init
  A new version is available (v0.2.0 => v0.2.2).
  Automatically upgrading to newer version v0.2.2..
  Checking for newer version
  Fetching newer version (v0.2.2)
  Running post-upgrade actions
  Upgrading host files
  Upgrading dependencies
  [###############-------------------------] 38% Upgrading dependencies

.. note:: This is an upgrade of Krill **Manager**, which *might not* include an
          upgrade of Krill, it may just be an upgrade to the Manager itself
          and/or to one or more of the other components such as NGINX or Rsync.
          The version number shown is the version number of Krill Manager, not
          of Krill.

Manual Upgrade
--------------

If you have previously completed the wizard and later run ``krillmanager init``
again, if a new version is available you will be offered the choice to upgrade:

.. code-block:: text

  # krillmanager init
  A new version is available (v0.2.0 => v0.2.2).

  > Would you like to upgrade? [YES/NO]: YES

The default action is to upgrade, but you can continue without upgrading.

Step by Step
------------

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
   wizard/https-certificates
   wizard/verifying-domains
   wizard/applying-settings
   wizard/setup-complete

.. history
.. authors
.. license
