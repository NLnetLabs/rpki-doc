.. _doc_krill_remote_publishing:

Registering with a Repository
=============================

The steps for registering a Krill CA as a publisher with a repository are very
similar to the steps to :ref:`register a Krill CA as a child of a parent CA<doc_krill_registering_with_a_parent>`.

Hosted vs 3rd Party
-------------------

Normally we advise that Krill CAs publish RPKI objects to a remote 3rd party
repository. However, you may want to allow delegated CAs (e.g. your customers,
or other business units) to publish at a repository server that you host and
manage. In addition, running the repository separately from the CA offers
flexibility in changing publication strategy and redundancy.

See :ref:`doc_krill_publication_server` for details on hosting your own RPKI 
repository.

Scenarios
---------

To register with an RPKI repository you must exchange :rfc:`8183` XML files
between the Krill CA and the repository service (which could also be a Krill
instance):

.. uml::

   @startuml
   skinparam sequenceMessageAlign center
   skinparam monochrome true
   skinparam ParticipantPadding 50

   participant "Admin" as admin
   participant "Krill CA" as ca
   participant "Repository" as repo
   
   admin <-[#black]- ca: Fetch **""<publisher_request/>"" XML**
   |||
   admin -[#black]> repo: Submit **""<publisher_request/> XML""**
   repo -> repo: Register publisher
   admin <[#black]- repo: Fetch **""<repository_response/>"" XML**
   |||
   admin -[#black]> ca: Submit **""<repository_response/>"" XML**
   
   @enduml

Depending on the service provider these actions may be possible via a browser
portal (e.g. :ref:`the Krill UI<doc_krill_using_ui_repository_setup>` or an
:ref:`NIR/RIR portal<member_portals>`) or may require that you execute commands (e.g. between a Krill
CA and a Krill repository).


Krill CA -> NIR/RIR Remote Repository
"""""""""""""""""""""""""""""""""""""

.. Note:: Registering a Krill CA as a publisher with a remote Krill repository
          may require that you log in to and interact with the NIR/RIR RPKI web
          portal.

#. In the Krill UI copy/download the :rfc:`8183` Publisher Request.

#. In the NIR/RIR portal:

   * Paste/upload the :rfc:`8183` Publisher Request.

   * Copy/download the :rfc:`8183` Repository Response.

#. In the Krill UI paste/upload the :rfc:`8183` Repository Response.


.. _remote_publishing_to_krill_repo:

Krill CA -> Krill Remote Repository
"""""""""""""""""""""""""""""""""""

Registering a Krill CA with a remote Krill repository can be done with the
following commands:

.. Note:: To complete the registration you will need the API token for the
          remote Krill repository, or the support of an admin willing to
          run commands against the remote Krill repository for you.

.. parsed-literal::

   Communicating with the CA server Krill instance:
   1. :ref:`krillc repo request<cmd_krillc_repo_request>`           Show RFC8183 Publisher Request XML.

   Communicating with the repository server Krill instance:
   2. :ref:`krillc publishers add<cmd_krillc_publishers_add>`         Add a publisher.
   3. :ref:`krillc publishers response<cmd_krillc_publishers_response>`    Show RFC8183 Repository Response XML.

   Communicating with the CA server Krill instance:
   4. :ref:`krillc repo update remote<cmd_krillc_repo_update>`     Change which repository this CA uses.


.. _register_with_embedded_repo:

Krill CA -> Krill Embedded Repository
"""""""""""""""""""""""""""""""""""""

Registering a Krill CA with a repository embedded in the same Krill instance as
the CA can be done with the following commands:

.. parsed-literal::

   On the CA/repository server:
   1. :ref:`krillc repo request<cmd_krillc_repo_request>`           Show RFC8183 Publisher Request.
   2. :ref:`krillc repo update embedded<cmd_krillc_repo_update>`   Change which repository this CA uses.

Working with Publishers and Repositories
----------------------------------------

See:
  - :ref:`krillc publishers list<cmd_krillc_publishers_list>`
  - :ref:`krillc publishers show<cmd_krillc_publishers_show>`
  - :ref:`krillc repo show<cmd_krillc_repo_show>`
  - :ref:`krillc bulk sync<cmd_krillc_bulk_sync>`

.. Warning:: The following commands require special care when using them on a
             previously established setup. Please consult the documentation for
             these commands before using them:

               - :ref:`krillc publishers remove<cmd_krillc_publishers_remove>`
               - :ref:`krillc repo update<cmd_krillc_repo_update>`