Manage CA(s)
============

In this section we will explain how to use the CLI to manage 'organisational' CAs
in Krill:

* Add a CA to Krill
* Add a parent to a CA
* Add a child to a CA
* Manage Route Authorizations (ROAs) in a CA
* View the status of a CA
* Perform a key roll

If you just want to try out Krill you can set "use_ta" to "true" in your krill.conf,
or use an env variable:

.. code-block:: text

   $ export KRILL_USE_TA="true"

You may also wish to set the following variables, so that you don't have to
repeat command line arguments for these:

.. code-block:: text

   $ export KRILL_CLI_SERVER="https://localhost:3000/"
   $ export KRILL_CLI_TOKEN="change-the-combination-on-my-luggage"
   $ export KRILL_CLI_MY_CA="ca"

In the examples below we assume that the ENV variables are set and we omit the
equivalent arguments.


Add a CA
""""""""

When adding a CA you need to choose a "handle", essentially just a name. The term "handle"
comes from RFC 8183 and is used in the communication protocol between CAs and CAs and
publication servers.

Example:

.. code-block:: text

   $ krillc add


The equivalent API call:

.. code-block:: text

   POST: https://localhost:3000/api/v1/cas
   Headers: Bearer: secret
   Body: {"handle":"ca"}


API Response: <empty> (Status: 200)

When a CA has been added, it is registered to publish locally in the Krill instance where
it exists, but other than that it has no configuration yet. In order to do anything useful
with a CA you will first have to add at least one parent to it, and then most likely
some Route Authorizations and/or Child CAs.


List CAs
""""""""

You can list all handles (names) for the existing CAs in krill using the following
command:

.. code-block:: text

   $ krillc list
   ta
   ca


The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas
   Headers: Bearer: secret


Example API Response:

.. code-block:: json

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

   $ krillc show --ca ta
   Name:     ta
   
   Base uri: rsync://localhost:3000/repo/ta/
   RRDP uri: https://localhost:3000/rrdp/notification.xml
   
   Parents:
   ta This CA is a TA
   Resource Class: 0
   Parent: ta
   State: active
       Resources:
       ASNs: AS0-AS4294967295
       IPv4: 0.0.0.0/0
       IPv6: ::/0
   Current objects:
     6E4ADECC02095CCC26D98C36717CFAF3A5AC506E.crl
     6E4ADECC02095CCC26D98C36717CFAF3A5AC506E.mft
   
   Children:
   <none>

Or for your new CA:

.. code-block:: text

   $ krillc show

   Name:     ca
   
   Base uri: rsync://localhost:3000/repo/ca/
   RRDP uri: https://localhost:3000/rrdp/notification.xml
   
   Parents:
   <none>
   Children:
   <none>

The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas/ca
   Headers: Bearer: secret

API response:

.. code-block:: text

   {
     "handle": "ca",
     "repo_info": {
       "base_uri": "rsync://localhost/repo/ca/",
       "rpki_notify": "https://localhost:3000/rrdp/notification.xml"
     },
     "parents": [],
     "resources": {},
     "children": []
   }


Add a Child to the embedded TA
""""""""""""""""""""""""""""""

If you are using an embedded TA for testing then you will first need to add your new
CA "ca" to it. Krill supports two communication modes:

1. embedded, meaning the both the parent and child CA live in the same Krill
2. rfc6492, meaning that the official RFC protocol is used

Here we will document the second option. It's slightly less efficient, but it's the
same as what you would need to delegate from your CA to remote CAs.

Step 1: RFC 8183 request XML
---------------------------

First you will need to get the RFC 8183 request XML from your child:

.. code-block:: text

   $ krillc parents myid > myid.xml

The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas/ca/child_request
   Headers: content-type: application/xml
   Headers: Bearer: secret

API Response: RFC 8183 request XML

Step 2: Add child "ca" to "ta"
------------------------------

To add a child, you will need to:
  1. choose a unique local name (handle) that the parent will use for the child
  2. choose initial resources (asn, ipv4, ipv6)
  3. have an RFC 8183 request

And in this case we also need to override the ENV variable and indicate that we
want to add this child to the CA "ta". The following command will add the child,
and the RFC 8183 XML from the "ta":

.. code-block:: text

   $ krillc children add --ca ta \
                         --child ca \
                         --ipv4 "10.0.0.0/8" --ipv6 "2001:DB8::/32" \
                         --rfc8183 myid.xml > parent-res.xml

However, if you are using the API, then you need to send the RFC 8183 request as
an equivalent JSON structure (the CLI does this under the hood):

.. code-block:: text

   POST: https://localhost:3000/api/v1/cas/ta/children
   Headers: Bearer: secret
   Body: {
      "handle":"ca",
      "resources": {
          "asn":"",
          "v4":"10.0.0.0/8",
          "v6":"2001:db8::/32"
      },
      "auth": {
          "Rfc8183": {
              "tag": null,
              "child_handle": "ca",
              "id_cert": "BASE64 of DER encoded X509 certificate"
          }
      }
  }

The default response is the RFC 8183 parent response XML file. Or, if you set
`--format json` you will get the plain API reponse:

.. code-block:: text

   {
     "Rfc6492": {
       "tag": null,
       "id_cert": "BASE64 of parent ID certificate",
       "parent_handle": "ta",
       "child_handle": "ca",
       "service_uri": {
         "Https": "https://localhost:3000/rfc6492/ta"
       }
     }
   }


If you need the response again, you can ask the "ta" again:

.. code-block:: text

   $ krillc children response --ca "ta" --child "ca"

The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas/ta/children/ca/contact
   Headers: Bearer: secret


Step 3: Add parent "ta" to "ca"
-------------------------------

You can now add "ta" as a parent to your CA "ca". You need to choose a locally
unique handle that your CA will use to refer to this parent. Here we simply use
the handle "ta" again, but in case you have multiple parents you may want to
refer to them by names that make sense in your context.

Note that whichever handle you choose, your CA will use the handles that the
parent response included for itself *and* for your CA in its comminication with
this parent. I.e. you may want to inspect the response and use the same handle
for the parent (parent_handle attribute), and do not be surprised or alarmed if
the parent refers to your ca (child_handle attribute) by some seemingly random
name. Some parents do this to ensure unicity.

.. code-block:: text

   $ krillc parents add --parent ripencc --rfc8183 ./parent-res.xml

The equivalent API call:

.. code-block:: text

   POST: https://localhost:3000/api/v1/cas/ca/parents
   Headers: Bearer: secret
   Body: {
      "handle": "ripencc",
      "contact": {
         "Rfc6492": {
            "tag": null,
            "id_cert": "BASE64 of parent ID cert",
            "parent_handle": "ta",
            "child_handle": "ca",
            "service_uri": "Https":"https://localhost:3000/rfc6492/ta"
          }
        }
      }

Now you should see that your "child" is certified:

.. code-block:: text

   $ krillc show
   Name:     ca
   
   Base uri: rsync://localhost:3000/repo/ca/
   RRDP uri: https://localhost:3000/rrdp/notification.xml
   
   Parents:
   ripencc RFC 6492 Parent
   Resource Class: 0
   Parent: ripencc
   State: active
       Resources:
       ASNs: 
       IPv4: 10.0.0.0/8
       IPv6: 2001:db8::/32
   Current objects:
     48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.crl
     48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.mft
   
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

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

   # Some comment
     # Indented comment

   A: 10.0.0.0/24 => 64496
   A: 10.1.0.0/16-20 => 64496   # Add prefix with max length
   R: 10.0.3.0/24 => 64496      # Remove existing authorization

You can then add this to your CA:

.. code-block:: text

   $ krillc roas update --delta ./roas.txt
   Status: 400 Bad Request, Error: {"code":2402,"msg":"Invalid ROA delta: removing a definition which is unknown"}

And as you can see Krill gives an error because you cannot remove authorization "10.0.3.0/24 => 64496" as you do not have it.

If you remove the "R:" line and submit again, then you should see no response, and no
error.

The API equivalent for sending updates uses JSON rather than the above text format:

.. code-block:: text

   POST: https://localhost:3000/api/v1/cas/ca/routes
   Headers: Bearer: secret
   Body: {
       "added": [
           {
               "asn": 64496,
               "prefix": "10.0.0.0/24"
           },
           {
               "asn": 64496,
               "prefix": "10.1.0.0/16",
               "max_length": 20
           }
       ],
       "removed": [
           {
               "asn": 64496,
               "prefix": "10.0.3.0/24"
           }
       ]
    }


List Route Authorizations
"""""""""""""""""""""""""

You can list Route Authorizations as well:

.. code-block:: text

   $ krillc roas list
   10.0.0.0/24 => 64496
   10.1.0.0/16-20 => 64496

The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas/ca/routes
   Headers: Bearer: secret

API JSON response:

.. code-block:: text

   $ krillc roas list --format json
   [
       {
           "asn": 64496,
           "prefix": "10.0.0.0/24"
       },
       {
           "asn": 64496,
           "prefix": "10.1.0.0/16",
           "max_length": 20
       }
   ]



History
"""""""

You can show the history of all the things that happened to your CA:

.. code-block:: text

   $ krillc history
   id: ca version: 0 details: Initialised with cert (hash): 973e3e967ecb2a2a409a785d1faf61cf73a66044, base_uri: rsync://localhost:3000/repo/ca/, rpki notify: https://localhost:3000/rrdp/notification.xml
   id: ca version: 1 details: added RFC6492 parent 'ripencc' 
   id: ca version: 2 details: added resource class with name '0'
   id: ca version: 3 details: requested certificate for key (hash) '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' under resource class '0'
   id: ca version: 4 details: activating pending key '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' under resource class '0'
   id: ca version: 5 details: added route authorization: '10.1.0.0/16-20 => 64496'
   id: ca version: 6 details: added route authorization: '10.0.0.0/24 => 64496'
   id: ca version: 7 details: updated ROAs under resource class '0' added: 10.1.0.0/16-20 => 64496 10.0.0.0/24 => 64496 
   id: ca version: 8 details: updated objects under resource class '0' key: '48C9F037625B3F5A6B6B9D4137DB438F8C1B1783' added: 31302e312e302e302f31362d3230203d3e203634343936.roa 31302e302e302e302f3234203d3e203634343936.roa  updated: 48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.crl 48C9F037625B3F5A6B6B9D4137DB438F8C1B1783.mft  withdrawn: 

The equivalent API call:

.. code-block:: text

   GET: https://localhost:3000/api/v1/cas/ca/history
   Headers: Bearer: secret
