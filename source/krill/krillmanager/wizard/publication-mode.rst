.. _doc_krill_manager_wizard_publication_mode:

Wizard: Publication Mode
========================

.. Tip::
   See also the following Krill documentation:
     - :ref:`doc_krill_before_you_start`
     - :ref:`doc_krill_parent_interactions`
     - :ref:`doc_krill_publication_server`

Krill can operate in one of two modes:
  - Publish with a 3rd party
  - Publish in its own repository

The publication mode wizard page lets you choose which of these modes Krill
will be configured for:

.. code-block:: text

  KRILL SETUP WIZARD: Publication mode                          [next: CA name]
  -----------------------------------------------------------------------------

  Would you like to publish with a 3rd party (e.g. NIC.br), or run your own
  publication server?

  Info:
  - If you answer YES you will need to use your repository provider's portal
    to obtain details needed to permit Krill to publish to the repository.

  - If instead you answer NO, Krill's embedded repository functionality will
    be enabled and the repository data will be served for you from HTTP/RRDP
    and Rsync servers that will run on this Droplet.

  Warning: We advise against running your own repository as each additional
  repository server is one more server that Relying Parties must contact, and
  thus you should ensure that your repository server remains available and
  reachable at all times.

  > Would you like to publish with a 3rd party? [YES/NO]:

3rd Party Mode
--------------

In 3rd party mode your Krill instance will **only** be a Certificate Authority
and you will need to configure it to publish any ROAs with an external
repository, e.g. that of a parent such as NIC.br.

Answering ``YES`` will enable 3rd party mode.

Self-Publishing Mode
--------------------

In self-publishing mode the ROA objects created by your Krill instance will be
made available by Krill Manager to Internet clients via the RRDP and Rsync
protocols.

Answering ``NO`` will enable self-publishing mode.

.. Note:: The wizard may need to ask you for additional information in later
          pages in order to complete the setup for self-publishing mode.
