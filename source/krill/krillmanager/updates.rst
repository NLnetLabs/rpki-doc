.. _doc_krill_manager_udpates:

Updates
=======

News
----

To stay informed about new releases of Krill and Krill Manager and to learn
about documentation updates and other announcements, subscribe to the 
`NLnet Labs RPKI mailing list <https://lists.nlnetlabs.nl/mailman/listinfo/rpki>`_.

Feedback
--------

If you would like to suggest an improvement or to report a problem, please
create an isse in the `Krill <https://github.com/NLnetLabs/krill/issues/new/choose>`_
or `Krill Manager <https://github.com/NLnetLabs/krillmanager/issues/new/choose>`_
GitHub issue tracker as appropriate.

Upgrading
---------

Krill Manager is able to upgrade itself and the components that it manages
such as Krill, NGINX and Rsync.

To upgrade Krill Manager do one of the following:

- Use the ``krillmanager upgrade`` CLI command.
- Answer ``YES`` when notified by the ``krillmanager init`` command that a new version is available.
- Automatically by ``krillmanager init`` if a new version is available and :ref:`doc_krill_manager_initial_setup` is not yet complete.
