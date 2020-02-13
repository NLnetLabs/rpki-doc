Managing your CA
================

In this section we will explain how to use the CLI to manage 'organisational'
CAs in Krill:

  * Add a CA to Krill
  * Add a parent to a CA
  * Add a child to a CA
  * Manage Route Authorisations (ROAs) in a CA
  * View the status of a CA
  * Perform a key roll

You may also wish to set the following variables, so that you don't have to
repeat command line arguments for these:

.. code-block:: text

   $ export KRILL_CLI_SERVER="https://localhost:3000/"
   $ export KRILL_CLI_TOKEN="correct-horse-battery-staple"
   $ export KRILL_CLI_MY_CA="Acme-Corp-International"

In the examples below we assume that the ENV variables are set and we omit the
equivalent arguments.


Adding Your CA
--------------

When adding a CA you need to choose a handle, essentially just a name. The
term "handle" comes from RFC 8183 and is used in the communication protocol
between parent and child CAs, as well as CAs and publication servers. For the
handle you can use alphanumerical characters, dashes or underscores.

The handle you select is not published in the RPKI but used as identification to
parent and child CAs you interact with. You should choose a handle that helps
others recognise your organisation. Once set, the handle cannot be be changed
as it would interfere with the communication between parent and child CAs, as
well as the publication repository.

.. code-block:: text

  $ krillc add

API Call: :krill_api_ca_post:`POST /v1/cas <cas>`

When a CA has been added, it is registered to publish locally in the Krill
instance where it exists, but other than that it has no configuration yet. In
order to do anything useful with a CA you will first have to add at least one
parent to it, followed by some Route Origin Authorisations and/or child CAs.

Listing CAs
-----------

You can list all handles (names) for the existing CAs in krill using the
following command:

.. code-block:: text

  $ krillc list
  Acme-Corp-International

API Call: :krill_api_ca_get:`GET /v1/cas <cas>`

Show CA Details
---------------

You can use the following command to show the details of your new CA:

.. code-block:: text

  $ krillc show
  Name:     Acme-Corp-International

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

API Call: :krill_api_ca_get:`GET /v1/cas/ca <cas~1{ca_handle}>`

Adding a Parent to your CA
--------------------------

You can now add a parent to your Certificate Authority. You do this by
generating a request file, which needs to be provided to your parent. Each RIR
and some NIRs offer the option to run Delegated RPKI. You will need to log into
their respective portals, upload the XML request file and in return you will
receive a response file which you need to supply to Krill.

* AFRINIC: https://my.afrinic.net/
* APNIC: https://myapnic.net
* ARIN: https://account.arin.net/
* LACNIC: http://milacnic.lacnic.net/
* RIPE NCC: https://my.ripe.net/

Step 1: Get the Request XML File
""""""""""""""""""""""""""""""""

First you will need to get the RFC 8183 request XML from your child.

.. code-block:: text

  $ krillc parents myid > myid.xml

API Call: :krill_api_ca_get:`GET /v1/cas/ca/child_request.json <cas~1{ca_handle}~1child_request.{format}>`

.. Warning:: ARIN does not support the RFC 8183 key exchange format yet, but
             they do have it `on their roadmap
             <https://www.arin.net/participate/community/acsp/suggestions/2020-3/>`_.
             You can still configure Delegated RPKI by transforming your request
             XML using `this XSL file
             <https://raw.githubusercontent.com/dragonresearch/rpki.net/master/potpourri/oob-translate.xsl>`_
             or `this form
             <https://sed.js.org/?gist=3f08fb293c8825855bb26f2865161575>`_
             before uploading it to ARIN.

Step 2: Add a Parent to Your CA
"""""""""""""""""""""""""""""""

You can now add parent to your CA "Acme-Corp-International". You need to choose
a locally unique handle that your CA will use to refer to this parent. Here we
use the handle "ripencc" as an example.

Note that whichever handle you choose, your CA will use the handles that the
parent response included for itself *and* for your CA in its communication with
this parent. I.e. you may want to inspect the response and use the same handle
for the parent (parent_handle attribute), and do not be surprised or alarmed if
the parent refers to your ca (child_handle attribute) by some seemingly random
name. Some parents do this to ensure unicity.

.. code-block:: text

  $ krillc parents add --parent ripencc --rfc8183 ./parent-res.xml

API Call: :krill_api_ca_post:`POST /v1/cas/ca/parents <cas~1{ca_handle}~1parents>`

Now you should see that your "child" is certified:

.. code-block:: text

  $ krillc show
  Name:     Acme-Corp-International

  Base uri: rsync://rsync.rpki.example.net/repo/ca/
  RRDP uri: https://rrdp.rpki.example.net/rrdp/notification.xml

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
      ASNs: 64496
      IPv4: 192.0.2.0/24
      IPv6: 2001:db8::/32

  Parents:
  Handle: ripencc Kind: RFC 6492 Parent

  Resource Class: 0
  Parent: ripencc
  State: active    Resources:
      ASNs: 64496
      IPv4: 192.0.2.0/24
      IPv6: 2001:db8::/32
  Current objects:
    553A7C2E751CA0B04B49CB72E30EB5684F861987.crl
    553A7C2E751CA0B04B49CB72E30EB5684F861987.mft

  Children:
  <none>

API Call: :krill_api_ca_get:`GET /v1/cas/ca <cas~1{ca_handle}>`

ROAs
----

Krill lets users create Route Origin Authorisations (ROAs), the signed objects
that state which Autonomous System (AS) is authorised to originate one of your
prefixes, along with the maximum prefix length it may have.

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

   # Some comment
     # Indented comment

   A: 192.0.2.0/24 => 64496
   A: 2001:db8::/32-48 => 64496   # Add prefix with max length
   R: 198.51.100.0/24 => 64496    # Remove existing authorisation

You can then add this to your CA:

.. code-block:: text

 $ krillc roas update --delta ./roas.txt

API Call: :krill_api_route_post:`POST /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`

If you followed the steps above then you would get an error, because there is no
authorisation for ``198.51.100.0/24 => 64496``. If you remove the line and
submit again, then you should see no response and no error.

You can list Route Origin Authorisations as well:

.. code-block:: text

  $ krillc roas list
  192.0.2.0/24 => 64496
  2001:db8::/32-48 => 64496

API Call: :krill_api_route_get:`GET /v1/cas/ca/routes <cas~1{ca_handle}~1routes>`


History
-------

You can show the history of all the things that happened to your CA:

.. code-block:: text

  $ krillc history
  id: ca version: 0 details: Initialised with ID key hash: 69ee7ef4dae43cd1dcd9ee65b8a1c7fd0c2499c3
  id: ca version: 1 details: added RFC6492 parent 'ripencc'
  id: ca version: 2 details: added resource class with name '0'
  id: ca version: 3 details: requested certificate for key (hash) 'D5EE85EF047010771547FE3ACFE4316503B8EC6F' under resource class '0'
  id: ca version: 4 details: activating pending key 'D5EE85EF047010771547FE3ACFE4316503B8EC6F' under resource class '0'
  id: ca version: 5 details: added route authorization: '192.0.2.0/24 => 64496'
  id: ca version: 6 details: added route authorization: '2001:db8::/32 => 64496'


API Call: :krill_api_ca_get:`GET /v1/cas/ca/history <cas~1{ca_handle}~1history>`
