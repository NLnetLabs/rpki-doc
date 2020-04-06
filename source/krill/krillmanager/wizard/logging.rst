.. _doc_krill_manager_logging:

Logging
=======

In this page of the wizard you can optionally choose to upload host and Krill
Manager deployed Docker container logs to an external `AWS S3 (Amazon Web Services Simple Storage Service) <https://aws.amazon.com/s3/>`_
compatible service. The upload also includes Krill RFC protocol exchange logs.

This could be AWS S3 itself, or a service such as `DigitalOcean Spaces <https://www.digitalocean.com/products/spaces/>`_.
Many such services exist. For an incomplete list consult the `Amazon S3 Wikipedia article <https://en.wikipedia.org/w/index.php?title=Amazon_S3&section=7#S3_API_and_competing_services>`_.

Answering ``NO`` will cause the wizard to skip to the next page.

.. Warning:: If you do not choose to upload logs they are still available to
             you but in the event that the host suffers a failure you will lose
             these logs unless you capture them as part of a periodic backup
             process.

Upload frequency
----------------

RFC protocol exchange logs are uploaded hourly. All other logs are uploaded at
least every few minutes, more frequently if there is a lot of logging activity.

Log retention
-------------

When log upload is enabled, local copies of Krill RFC audit logs are deleted
after two days as these logs can become quite large.

All other logs are rotated according to the `systemd` journal default behaviour.

Upload structure
----------------

Logs are uploaded to a structure similar to the following:

.. code-block:: bash
 
   <base_dir>/rfc_trail
   <base_dir>/YYYMMDDHH/<hostname>/<source>.gz
   <base_dir>/YYYMMDDHH/<hostname>/<container>/<instance id>.<N>.gz

Where ``<base_dir>`` is a directory of your choosing.

----

**TO DO** Complete this page.