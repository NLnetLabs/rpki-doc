.. _doc_krill_manager_wizard_restore_from_backup:

Restore from backup
===================

Without Prior Backups
---------------------

In most cases the restore from backup wizard page will look like this:

.. figure:: img/restore-from-backup-1.png
   :alt: Wizard restore from backup page screenshot.

   A Krill Manager instance with no backup archives present.

With Prior Backups
------------------

If however you have used the ``krillmanager backup`` command you will
see those backup archives listed on this page:

.. figure:: img/restore-from-backup-2.png
   :alt: Wizard welcome page screenshot.

   A Krill Manager instance with backup archives present.

Initialize or Restore
---------------------

Either:

1. Accept the default action ``INITIALIZE`` to continue setting up Krill Manager
   from scratch.
2. Alternatively, enter the number of a listed backup or type ``FILE`` to enter
   the path on disk to a backup archive to restore (e.g. that you have previously
   copied to the system with the ``scp`` command).