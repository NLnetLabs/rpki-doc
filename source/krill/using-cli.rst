.. WARNING::  The CLI has grown organically and will most likely see an overhaul
              before Krill 1.0 is released. Functionality will remain mostly the
              same, but subcommands and options will change.

Using the Krill CLI
===================

The Krill CLI is a wrapper around the Krill API which is based on Json over HTTPS.


Certificate Authority
---------------------

The term Certificate Authority (CA) cab sometimes be a bit overloaded, so it's worth
defining it better.

In the context of Krill we refer to a CA as unit that represents an organisational unit,
e.g. your company. This CA will typically have a single parent Certificate Authority, like
the RIR/NIR that you have registered IP addresses and/or AS numbers with. However, you
may have multiple parents. It's also possible to delegate resources down children of your
own, e.g. business units, departments, members or clients.

Resources that you receive from each of your parents will each go on separate X509
certificates, and in fact you might even get resources from a single parent assigned to
you on different certificates. These certificates are often referred to as "CA certificates",
which can be somewhat confusing with regards to the term CA. A "CA certificate" is simply
a certificate that is allowed to sign delegated certificates in the RPKI. And an 
'organisational' CA, as described above, will typically have one or many CA certificates.

So, in the context of Krill we always talk about 'organisational' CAs when we talk about
CAs. In fact the main reason of being for Krill is that it let's you think about your
organisation at this higher level, while Krill will deal with the management of lower
level CA certificates, and all the other moving parts that are used in the RPKI.


General use
-----------

To use the CLI you will need to specify the ``server``, ``token`` and ``format``
you would like to use:

.. code-block:: bash

   krill_admin -s https://<yourhost:port>/ -t <token> -f text|json|none

The default format is json, but for human operators this can be suboptimal. We
may therefore change the default to 'text' in future. We also plan to introduce
either some local config file (e.q. ~/.krill_admin.cfg) or ENV variables that
allow an operator to set defaults for this in future.

For now though, you will need to specify this whenever you call the CLI, or
perhaps create an alias..


Help
----

To access the help function of the CLI use the word 'help', e.g.:

.. code-block:: text

   $ ./target/debug/krill_admin --server https://localhost:3000/ --token secret --format text help
   Krill admin client 0.1.0

   USAGE:
       krill_admin [OPTIONS] --server <URI> --token <token-string> [SUBCOMMAND]

   FLAGS:
       -h, --help       Prints help information
       -V, --version    Prints version information

   OPTIONS:
       -f, --format <type>           Specify the report format (none|json|text|xml). If left unspecified the format will
                                     match the corresponding server api response type.
       -s, --server <URI>            Specify the full URI to the krill server.
       -t, --token <token-string>    Specify the value of an admin token.

   SUBCOMMANDS:
       cas           Manage CAs
       health        Perform a health check. Exits with exit code 0 if all is well, exit code 1 in case of any issues
       help          Prints this message or the help of the given subcommand(s)
       publishers    Manage publishers
       rfc8181       Manage RFC8181 clients



Manage CA(s)
------------

The ``cas`` subcommand is used to manage 'organisational' CAs in Krill:

* Add a CA to Krill
* Add a parent to a CA
* Add a child to a CA
* Manage Route Authorizations in a CA
* View the status of a CA
* Perform a key roll


Add a CA
""""""""

When adding a CA you need to choose a "handle", essentially just a name. The term "handle"
comes from RFC 8183 and is used in the communication protocol between CAs and CAs and
publication servers.

Furthermore you need to chose a "token", or password, to manage this
specific CA. For the moment though, this "token" is not used in practice. Krill is managed
entirely using a single master "token". However, this may well change in future, especially
if operators tell us that they want to support multiple CAs in a single Krill instance,
while delegating authority to different users. If this is a use case you have, please talk
to us!

Example:

.. code-block:: bash

   $ krill_admin --server https://localhost:3000/ --token secret --format text \
   cas add --handle child --token child
   

When a CA has been added, it is registered to publish locally in the Krill instance where
it exists, but other than that it has no configuration yet. In order to do anything useful
 with a CA you will first have to add at least one parent to it, and then most likely 
 some Route Authorizations and/or Child CAs.


List CAs
""""""""

You can look at the existing CAs in krill using the following command:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text cas list
   ta
   child


.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format json cas list
   {
     "cas": [
       {
         "name": "ta"
       },
       {
         "name": "child"
       }
     ]
   }


Show CA Details
"""""""""""""""

You can use the following to show the details of the embedded TA, if you enabled it:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text \
   cas show --handle ta
   
   Name:     ta

   Base uri: rsync://localhost/repo/ta/
   RRDP uri: https://localhost:3000/rrdp/notification.xml

   Parent:  This CA is a TA
   Resource Class: 0
   State: active
       Resources:
       ASNs: AS0-AS4294967295
       IPv4: 0.0.0.0/0
       IPv6: ::/0
   Current objects:
     CED18E1CDFA208EBA4A400DCBB3B6B7D609BBC8E.mft
     CED18E1CDFA208EBA4A400DCBB3B6B7D609BBC8E.crl

   Children:
   <none>


Or for your new CA:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text \
   cas show --handle child
   
   Name:     child
   
   Base uri: rsync://localhost/repo/child/
   RRDP uri: https://localhost:3000/rrdp/notification.xml
   
   Children:
   <none>



Add a Child to the embedded TA
""""""""""""""""""""""""""""""

If you are using an embedded TA for testing then you will first need to add your new
CA "child" to it. Krill supports two communication modes:
1) embedded, meaning the both the parent and child CA live in the same Krill
2) rfc6492, meaning that the official RFC protocol is used

Here we will document the second option. It's slightly less efficient, but it's the
same as what you would need to delegate from your CA to remote CAs.

Step 1: Get the RFC8183 request XML from your child:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text \
   cas rfc8183_child_request --handle child > ./request.xml

Step 2: Now give the child request XML to the parent, and get its response:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text \
   cas children --handle ta add -4 10.0.0.0/8 rfc6492 --xml ./child-request.xml \
   >./parent-response.xml


Step 3: Now add the TA as a parent to child

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text cas \
   update --handle child add-parent --parent ta rfc6492 --xml ./parent-response.xml 

Now you should see that your "child" is certified:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text cas \
   show --handle child
   
   Name:     child
   
   Base uri: rsync://localhost/repo/child/
   RRDP uri: https://localhost:3000/rrdp/notification.xml
   
   Parent:  RFC 6492 Parent
   Resource Class: 0
   State: active
       Resources:
       ASNs: 
       IPv4: 10.0.0.0/8
       IPv6: 
   Current objects:
     2E0AD62800126C5D1F6388CD89B540F24B28C0ED.mft
     2E0AD62800126C5D1F6388CD89B540F24B28C0ED.crl

   Children:
   <none>


Add a real CA as your parent
""""""""""""""""""""""""""""

Similar to above, except that you only need to generate the XML in step 1, hand it over
to your parent CA through whatever function they provide, and then get the response.xml
from them and add it your child as described in step 3.


ROAs
""""

At this point you probably want to manage some ROAs!

Krill lets users configure Route Authorizations, i.e. the intent to authorise a Prefix you
hold, up to a maximum length to be announced by an ASN. Krill will make sure that the actual
ROA objects are created. Krill will also refuse to accept authorizations for prefixes you
don't hold.


Update ROAs
"""""""""""

We will add Json support later, but for the moment Krill expects ROA updates in the
following file format:

.. code-block:: text
   # Some comment
     # Indented comment

   A: 192.168.1.0/24 => 64496
   A: 192.168.0.0/16-20 => 64496   # Add prefix with max length
   R: 192.168.3.0/24 => 64496      # Remove existing authorization

You can then add this to your CA:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text cas \
   roas --handle child update --delta ./data/authorise.txt 
   

List Route Authorizations
"""""""""""""""""""""""""

You can list Route Authorizations as well:

.. code-block:: text

   $ krill_admin --server https://localhost:3000/ --token secret --format text cas \
   roas --handle child list
   
   10.0.0.0/20-24 => 64496
   10.1.0.0/24 => 64496




























