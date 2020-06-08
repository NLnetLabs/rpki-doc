.. _doc_krill_registering_with_a_parent:

Registering with a Parent
=========================

As mentioned in :ref:`doc_krill_parent_interactions` in order to join the RPKI
you will need to register your CA with a parent in the hierarchy. Typically that
will be an RIR or NIR, but as mentioned in the :ref:`introduction<doc_krill>` it
could also be a parent within your business.

To register with an RPKI parent you need to exchange :rfc:`8183` XML files
with the parent via their :ref:`web portal<member_portals>` or API.

.. uml::

   @startuml
   skinparam sequenceMessageAlign center
   skinparam monochrome true
   skinparam ParticipantPadding 50

   participant "Admin" as admin
   participant "Krill CA" as ca
   participant "Parent CA" as parent
   
   admin <-[#black]- ca: Fetch **""<child_request/>"" XML**
   |||
   admin -[#black]> parent: Submit **""<child_request/> XML""**
   parent -> parent: Register child
   admin <[#black]- parent: Fetch **""<parent_response/>"" XML**
   |||
   admin -[#black]> ca: Submit **""<parent_response/>"" XML**
   
   @enduml


Scenarios
---------

Krill CA -> NIR/RIR Remote CA
"""""""""""""""""""""""""""""

Registering a Krill CA as a child with a remote Krill CA may require that you
log in to and interact with the a parent web portal, e.g.:

#. In the Krill UI copy/download the :rfc:`8183` Child Request.

#. In the NIR/RIR portal:

   * Paste/upload the :rfc:`8183` Child Request.

   * Copy/download the :rfc:`8183` Parent Response.

#. In the Krill UI paste/upload the :rfc:`8183` Parent Response.


Krill CA -> Krill Remote CA
"""""""""""""""""""""""""""

Registering a Krill CA as a child of a remote Krill CA can be done with the
following commands:

.. Note:: To complete the registration you will need the API token for the
          remote Krill CA, or the support of an admin willing to run commands
          against the remote Krill CA for you.

.. parsed-literal::

   Communicating with the CA server Krill instance:
   1. :ref:`krillc parents request<cmd_parents_request>`        Show RFC8183 Child Request XML.

   Communicating with the remote Krill CA instance:
   2. :ref:`krillc children add remote<cmd_children_add_remote>`    Add a child to a CA..
   3. :ref:`krillc children response<cmd_children_response>`      Show RFC8183 Parent Response XML.

   Communicating with the CA server Krill instance:
   4. :ref:`krillc parents add remote<cmd_parents_add_remote>`     Add a parent to this CA.


Krill CA -> Krill Local CA
""""""""""""""""""""""""""

Registering a Krill CA as a child of a local "embedded" Krill CA can be done
with the following commands:

.. parsed-literal::

   Communicating with the CA server Krill instance:
   1. :ref:`krillc parents add embedded<cmd_parents_add_embedded>`