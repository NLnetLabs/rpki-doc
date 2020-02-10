.. _column-content:

.. container:: left-col

Manage CA(s)
============

In this section we will explain how to use the CLI to manage 'organisational'
CAs in Krill:

* Add a CA to Krill
* Add a parent to a CA
* Add a child to a CA
* Manage Route Authorizations (ROAs) in a CA
* View the status of a CA
* Perform a key roll

If you just want to try out Krill you can set "use_ta" to "true" in your
`krill.conf`, or use an env variable:

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

When adding a CA you need to choose a handle, essentially just a name. The
term "handle" comes from RFC 8183 and is used in the communication protocol
between parent and child CAs, as well as CAs and publication servers. For the
handle you can use alphanumerical characters, dashes or underscores. 

The handle you select is not published in the RPKI but used as identification to
parent and child CAs you interact with. You should choose a handle that helps 
others recognise your organisation. Once set, the handle canot be be changed 
as it would interfere with the communication between parent and child CAs, as 
well as the publication respository.

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc add

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_post:`POST /v1/cas <cas>`

When a CA has been added, it is registered to publish locally in the Krill instance where
it exists, but other than that it has no configuration yet. In order to do anything useful
with a CA you will first have to add at least one parent to it, and then most likely
some Route Authorizations and/or Child CAs.


List CAs
""""""""

You can list all handles (names) for the existing CAs in krill using the following
command:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc list
          ta
          ca

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas <cas>`

Show CA Details
"""""""""""""""

You can use the following to show the details of the embedded TA, if you enabled
it:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc show --ca ta
          Name:     ta

          Base uri: rsync://localhost/repo/ta/
          RRDP uri: https://localhost:3000/rrdp/notification.xml

          ID cert PEM:
          -----BEGIN CERTIFICATE-----
          MIIDPDCCAiSgAwIBAgIBATANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyg2MUE1
          QkIzNDBBMDM4M0U4NDdENjI0MThDQUMwOTIxQUJCN0M4NTU1MCAXDTE5MTIwMzEx
          ..
          Yge7BolTITNX8XBzDdTr91TgUKEtDEGlNh6sYOONJW9rQxZIsDIdTeBoPSQKCdXk
          D13RgMxQSjycIfAeIBo9yg==
          -----END CERTIFICATE-----

          Hash: 85041ff6bf2d8edf4e02c716e8be9f4dd49e2cc8aa578213556072bab75575ee

          Total resources:
              ASNs: AS0-AS4294967295
              IPv4: 0.0.0.0/0
              IPv6: ::/0

          Parents:
          Handle: ta Kind: This CA is a TA

          Resource Class: 0
          Parent: ta
          State: active    Resources:
              ASNs: AS0-AS4294967295
              IPv4: 0.0.0.0/0
              IPv6: ::/0
          Current objects:
            1529A3C0E47EA38C1101DECDD6330E932E3AB98F.crl
            1529A3C0E47EA38C1101DECDD6330E932E3AB98F.mft

          Children:
          <none>

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ta <cas~1{ca_handle}>`

Or for your new CA:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc show
          Name:     ca

          Base uri: rsync://localhost/repo/ca/
          RRDP uri: https://localhost:3000/rrdp/notification.xml

          ID cert PEM:
          -----BEGIN CERTIFICATE-----
          MIIDPDCCAiSgAwIBAgIBATANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyg2NTA1
          RDA4RUI5MTk5NkJFNkFERDNGOEYyQzUzQTUxNTg4RTY4NDJCMCAXDTE5MTIwMzEy
          ..
          zKtG5esZ+g48ihf6jBgDyyONXEICowcjrxlY5fnjHhL0jsTmLuITgYuRoGIK2KzQ
          +qLiXg2G+8s8u/1PW7PVYg==
          -----END CERTIFICATE-----

          Hash: 9f1376b2e1c8052c1b5d94467f8708935224c518effbe7a1c0e967578fb2215e

          Total resources: <none>

          Parents:
          <none>
          Children:
          <none>

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ca <cas~1{ca_handle}>`

Add a Child to the embedded TA
""""""""""""""""""""""""""""""

If you are using an embedded TA for testing then you will first need to add your
new CA "ca" to it. Krill supports two communication modes:

1. embedded, meaning the both the parent and child CA live in the same Krill
2. rfc6492, meaning that the official RFC protocol is used

Here we will document the second option. It's slightly less efficient, but it's
the same as what you would need to delegate from your CA to remote CAs.

Step 1: RFC 8183 request XML
----------------------------

First you will need to get the RFC 8183 request XML from your child:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc parents myid > myid.xml

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ca/child_request.json <cas~1{ca_handle}~1child_request.{format}>`

Step 2: Add child "ca" to "ta"
------------------------------

To add a child, you will need to:
  1. choose a unique local name (handle) that the parent will use for the child
  2. choose initial resources (asn, ipv4, ipv6)
  3. have an RFC 8183 request

And in this case we also need to override the ENV variable and indicate that we
want to add this child to the CA "ta". The following command will add the child,
and the RFC 8183 XML from the "ta":

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc children add --ca ta \
                                --child ca \
                                --ipv4 "10.0.0.0/8" --ipv6 "2001:DB8::/32" \
                                --rfc8183 myid.xml > parent-res.xml

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_post:`POST /v1/cas/ta/children <cas~1{ca_handle}~1children>`

The default response is the RFC 8183 parent response XML file. Or, if you set
`--format json` you will get the plain API reponse.

If you need the response again, you can ask the "ta" again:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc children response --ca "ta" --child "ca"

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ta/children/ca/contact <cas~1{ca_handle}~1children~1{child_handle}~1contact>`

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

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc parents add --parent ripencc --rfc8183 ./parent-res.xml

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_post:`POST /v1/cas/ca/parents <cas~1{ca_handle}~1parents>`

Now you should see that your "child" is certified:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc show
          Name:     ca

          Base uri: rsync://localhostrepo/ca/
          RRDP uri: https://localhost:3000/rrdp/notification.xml

          ID cert PEM:
          -----BEGIN CERTIFICATE-----
          MIIDPDCCAiSgAwIBAgIBATANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyg2NTA1
          RDA4RUI5MTk5NkJFNkFERDNGOEYyQzUzQTUxNTg4RTY4NDJCMCAXDTE5MTIwMzEy
          ..
          zKtG5esZ+g48ihf6jBgDyyONXEICowcjrxlY5fnjHhL0jsTmLuITgYuRoGIK2KzQ
          +qLiXg2G+8s8u/1PW7PVYg==
          -----END CERTIFICATE-----

          Hash: 9f1376b2e1c8052c1b5d94467f8708935224c518effbe7a1c0e967578fb2215e

          Total resources:
              ASNs: 
              IPv4: 10.0.0.0/8
              IPv6: 2001:db8::/32

          Parents:
          Handle: ripencc Kind: RFC 6492 Parent

          Resource Class: 0
          Parent: ripencc
          State: active    Resources:
              ASNs: 
              IPv4: 10.0.0.0/8
              IPv6: 2001:db8::/32
          Current objects:
            553A7C2E751CA0B04B49CB72E30EB5684F861987.crl
            553A7C2E751CA0B04B49CB72E30EB5684F861987.mft

          Children:
          <none>
          
    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ca <cas~1{ca_handle}>`

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

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

         $ krillc roas update --delta ./roas.txt

    .. tab-container:: api
       :title: api

       See: :krill_api_route_post:`POST /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`

If you followed the steps above then you would get an error, because there is no
authorization for 10.0.3.0/24 => 64496. If you remove the line and submit again,
then you should see no response, and no error.


List Route Authorizations
"""""""""""""""""""""""""

You can list Route Authorizations as well:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

       .. code-block:: text

          $ krillc roas list
          10.0.0.0/24 => 64496
          10.1.0.0/16-20 => 64496

    .. tab-container:: api
       :title: api

       See: :krill_api_route_get:`GET /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`


History
"""""""

You can show the history of all the things that happened to your CA:

.. content-tabs::

    .. tab-container:: cli
       :title: krillc

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

    .. tab-container:: api
       :title: api

       See: :krill_api_ca_get:`GET /v1/cas/ca/history <cas~1{ca_handle}~1history>`
