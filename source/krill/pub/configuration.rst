.. _doc_krill_pub_configuration:

.. Note::  This part of the project is currently being built. 
           Documentation will likely change significantly as the software evolves.

Configuration
=============

Before you can run the publication server, you first have to create the work
directories and copy the configuration file:

.. code-block:: bash

   mkdir data
   mkdir publishers
   cp defaults/krill.conf ./data

After these steps, edit your ``krill.conf`` file and, at least, set a secret 
token for the ``auth_token`` key, at the end of the file — or — set the 
KRILL_AUTH_TOKEN environment variable when you start ``krilld``. 

Other than that the defaults should be okay for local testing.