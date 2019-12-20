.. _doc_rpkivalidator3:


RIPE NCC RPKI Validator 3.1
===========================

Full-featured RPKI relying party software, written by the `RIPE NCC <http://ripe.net/>`_ in Java.
This application allows operators to download and validate the global RPKI data set for use in their BGP decision making
process and router configuration. 

The project consists of two separately deployable units called :ref:`RPKI Validator <rpki-validator>` and :ref:`RPKI-RTR Server <rpki-rtr-server>`.

.. toctree::
   :maxdepth: 2
   :name: toc-rpkivalidator3

   installation


.. _rpki-validator:

RPKI Validator
--------------

Set up to run as a daemon, and has the following features:

- Supports all current RPKI objects: certificates, manifests, CRLs, ROAs, router certificates and ghostbuster records
- Supports the RRDP delta protocol
- Supports caching RPKI data in case a repository is unavailable
- Uses an asynchronous strategy to retrieve (often delegated) repositories, so that unavailable repositories do not block validation
- Features an API
- Has a full UI
- Supports exceptions trough local filters and assertions

.. _rpki-rtr-server:

RPKI-RTR server
---------------

A separate daemon, that allows routers to connect using the RPKI-RTR protocol. 
It's set up as a separate instance because not everyone needs to run this, but more importantly, 
if you do need to run this then a separate daemon allows one to run more than one instance for redundancy
(it keeps state even when the validator is down).


For more information, check the `release notes <https://github.com/RIPE-NCC/rpki-validator-3/blob/master/rpki-validator/Changelog.txt>`_. 
You can also contribute to the project on `GitHub <https://github.com/RIPE-NCC/rpki-validator-3>`_.

.. _requirements:

System Requirements
-------------------

You will need a UNIX-like system with OpenJDK 8 or higher and rsync. 
You will also need at least 1.5GB available on your server (2GB in total if you also run the RPKI-RTR server). 
One (virtual) CPU should be enough. 
The repository objects are stored in a file-based database, rather than in memory, for which we recommend at least 10GB of available disk space.




.. history
.. authors
.. license