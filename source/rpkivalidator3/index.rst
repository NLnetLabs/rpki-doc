.. _doc_rpkivalidator3:


RIPE NCC RPKI Validator 3.1
===========================

A fully-featured RPKI relying party software, written by the `RIPE NCC <http://ripe.net/>`_ in Java.
This application allows operators to download and validate the global RPKI data set for use in their BGP decision making
process and router configuration. 

The project consists of two separate deployable units called the :ref:`RPKI Validator <rpki-validator>` and :ref:`RPKI-RTR Server <rpki-rtr-server>`.

.. toctree::
   :maxdepth: 2
   :name: toc-rpkivalidator3

   installation


.. _rpki-validator:

RPKI Validator
--------------

Set up to run as a daemon, and has the following features:

- Supports all current RPKI objects: certificates, manifests, CRLs, ROAs, router certificates, and ghostbuster records
- Supports the RRDP delta protocol
- Supports caching RPKI data in case a repository is unavailable
- Uses an asynchronous strategy to retrieve (often delegated) repositories, so that unavailable repositories do not block validation
- Features an API
- Has a full UI
- Supports exceptions trough local filters and assertions

.. _rpki-rtr-server:

RPKI-RTR Server
---------------

A separate daemon that implements RPKI to the Router protocol (RTR), allowing *validated prefix origin* data to be delivered to routers. The RPKI-RTR Server is set up as a separate deamon because not everyone needs to run it. Far more importantly, a separate daemon allows you to start multiple instances for redundancy.

For more information, check the `release notes <https://github.com/RIPE-NCC/rpki-validator-3/blob/master/rpki-validator/Changelog.txt>`_. 
You can also contribute to the project on `GitHub <https://github.com/RIPE-NCC/rpki-validator-3>`_.

.. _requirements:

System Requirements
-------------------

You will need a UNIX-like system with OpenJDK 8 or higher and rsync. 
You will also need at least 1.5GB of RAM available on your server (2GB in total if you also run the RPKI-RTR server). 
One (virtual) CPU should be enough. 
The repository objects are stored in a file-based database, rather than in memory, for which we recommend at least 10GB of available disk space.




.. history
.. authors
.. license
