.. _doc_rpkiclient:


OpenBSD's rpki-client
=====================

A simple RPKI relying party software, written by the `OpenBSD <https://www.openbsd.org/>`_ project in C.
This application allows operators to download and validate the global RPKI data set for use in their BGP decision making
process and router configuration. 

The project consists of a oneshot CLI utility which optionally can be coupled with a RTR server such as GoRTR or RTRTR.

.. toctree::
   :maxdepth: 2
   :name: toc-rpkiclient

   installation

.. _rpki-client:

rpki-client
--------------

Rpki-client is a oneshot command line utility, and has the following features:

- Supports all current RPKI objects: certificates, manifests, CRLs, ROAs, router certificates, and ghostbuster records
- Supports caching RPKI data in case a repository is unavailable
- Uses cryptographically valid manifests to perform garbage collection: invald objects are pruned from the filesystem.
- Supports generating output in OpenBGPD, BIRD, CSV, and JSON format.

System Requirements
-------------------

You will need a UNIX-like system.
You will also need at least 128MB of RAM available on your server.
One (virtual) CPU should be enough. 
The repository objects are stored in a file-based database, rather than in memory, for which we recommend at least 10GB of available disk space.

.. history
.. authors
.. license
