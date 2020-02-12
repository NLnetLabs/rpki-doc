Remote Publishing
=================

By default Krill CAs use an embedded repository server for the publication of
RPKI objects. However, you may want to use a repository server hosted by a third
party for your CA - this way you can outsource the burden of 24/7 availability
of the repository. Or, you may want to allow delegated CAs (e.g. your customers,
or other business units) to publish at a repository server that you manage.

In this section we will explain how to set up Krill to perform either of those
roles.

Krill as Repository Server
--------------------------

The repository functions for Krill can be accessed through the `publishers`
subcommand in the CLI:

.. code-block:: text

  $ krillc publishers --help
  krillc-publishers
  Manage publishers in Krill.

  USAGE:
      krillc publishers [SUBCOMMAND]

  FLAGS:
      -h, --help       Prints help information
      -V, --version    Prints version information

  SUBCOMMANDS:
    add         Add a publisher.
    help        Prints this message or the help of the given subcommand(s)
    list        List all publishers.
    remove      Remove a publisher.
    response    Show RFC8183 Repository Response for a publisher.
    show        Show details for a publisher.


List Publishers
---------------

You can list all publishers in Krill using the command below. Note that the
list of publishers will include any embedded Krill CAs as well as any possible
remote (RFC 8181 compliant) publishers.

.. code-block:: text

  $ krillc publishers list
  Publishers: ta, ca

API Call: :krill_api_pub_get:`GET /v1/publishers <publishers>`


Show Publisher Details
----------------------

You can show the full details of a publisher, including the files that they
published.

.. code-block:: text

  $ krillc publishers show --publisher ta
  handle: ta
  id: 5ce21ed116540a22c562f45dae8f2eb5a3c13ceebase uri: rsync://localhost/repo/ta/

API Call: :krill_api_pub_get:`GET /v1/publishers/ca <publishers~1{publisher_handle}>`

The default text output just shows the handle of the publisher, the hash of its
identity certificate key, and the rsync URI jail under which the publisher is
allowed to publish objects.

The JSON response includes a lot more information, including the files which
were published and the full ID certificate used by the publisher. Note that
even embedded Krill CAs will have such a certificate, even if they access the
repository server locally.

Remove a Publisher
------------------

You can remove publishers. If you do, then all of its content will be removed
as well and the publisher will no longer be allowed to publish.

Note that you can do this without the publisher's knowledge, nor consent, even
for embedded Krill CAs. With great power comes great responsibility. That said,
you can always add a publisher again (also embedded publishers), and once a
publisher can connect to your repository again, it should be able to figure out
that it needs to re-publish all its content (Krill CAs will always check for
this).

.. code-block:: text

  $ krillc publishers remove --publisher ca

API Call: :krill_api_pub_delete:`DELETE /v1/publishers/ca <publishers~1{publisher_handle}>`


Add a Publisher
---------------

In order to add a publisher you have to get its RFC 8183 Publisher Request XML,
and hand it over to the server:

.. code-block:: text

  $ krillc publishers add --publisher ca --rfc8183 ./data/ca-pub-req.xml

API Call: :krill_api_pub_post:`POST /v1/publishers <publishers>`


Show Repository Response
------------------------

In order to show the RFC 8183 Repository Response XML for a specific publisher
use the following:

.. code-block:: text

  $ krillc publishers response --publisher ca
  <repository_response xmlns="http://www.hactrn.net/uris/rpki/rpki-setup/" version="1" publisher_handle="ca" service_uri="https://localhost:3000/rfc8181/ca" sia_base="rsync://localhost/repo/ca/" rrdp_notification_uri="https://localhost:3000/rrdp/notification.xml">
    <repository_bpki_ta> repository server id certificate base64 </repository_bpki_ta>
  </repository_response>

API Call: :krill_api_pub_get:`GET /v1/publishers/ca/response.json <publishers~1{publisher_handle}~1response.{format}>`

Publish at a Remote Repository
------------------------------

Controlling your CA's repository server is done through the `repo` subcommand
of the CLI:

.. code-block:: text

  $ krillc repo --help
  krillc-repo
  Manage the repository for your CA.

  USAGE:
      krillc repo [SUBCOMMAND]

  FLAGS:
      -h, --help       Prints help information
      -V, --version    Prints version information

  SUBCOMMANDS:
    help       Prints this message or the help of the given subcommand(s)
    request    Show RFC8183 Publisher Request.
    show       Show current repo config.
    state      Show current repo state.
    update     Change which repository this CA uses.

Show repository for CA
----------------------

You can use the following to show which repository server your CA is using,
as well as what is has published at the location. Krill will issue an actual
``list`` query to the repository and give back the response, or an error in case
of issues.

.. code-block:: text

   $ krillc repo show
   Repository Details:
     type:        embedded
     base_uri:    rsync://localhost/repo/ca/
     rpki_notify: https://localhost:3000/rrdp/notification.xml

   Currently published:
     c6e130761ccf212aea4038e95f6ffb3029afac3494ffe5fde6eb5062c2fa37bd rsync://localhost/repo/ca/0/281E18225EE6DCEB8E98C0A7FB596242BFE64B13.mft
     557c1a3b7a324a03444c33fd010c1a17540ed482faccab3ffe5d0ec4b7963fc8 rsync://localhost/repo/ca/0/31302e302e3132382e302f32302d3234203d3e20313233.roa
     444a962cb193b30dd1919b283ec934a50ec9ed562aa280a2bd3d7a174b6e1336 rsync://localhost/repo/ca/0/281E18225EE6DCEB8E98C0A7FB596242BFE64B13.crl
     874048a2df6ff1e63a14e69de489e8a78880a341db1072bab7a54a3a5174057d rsync://localhost/repo/ca/0/31302e302e302e302f32302d3234203d3e20313233.roa

API Call: :krill_api_ca_get:`GET /v1/cas/ca/repo <cas~1{ca_handle}~1repo>`


Show Publisher Request
----------------------

You can use the following to show the RFC 8183 Publisher Request XML for a CA. You
will need to hand this over to your remote repository so that they can add your
CA.

.. code-block:: text

  $ krillc repo request
  <publisher_request xmlns="http://www.hactrn.net/uris/rpki/rpki-setup/" version="1" publisher_handle="ca">
    <publisher_bpki_ta>your CA ID cert DER in base64</publisher_bpki_ta>
  </publisher_request>

API Call: :krill_api_ca_get:`GET /v1/cas/ca/repo/request.json <cas~1{ca_handle}~1repo~1request.{format}>`


Change Repository for a CA
--------------------------

You can change which repository server is used by your CA. If you have multiple
CAs you will have to repeat this for each of them. Also, note that by default
your CAs will assume that they use the embedded publication server. So, in order
to use a remote server you will have to use this process to change over.

Changing repositories is actually more complicated than one might think, but
fortunately it's all automated. When you ask Krill to change, the following
steps will be executed:

* check that the new repository can be reached, and this ca is authorised
* regenerate all objects using the URI jail given by the new repository
* publish all objects in the new repository
* request new certificates from (all) parent CA(s) including the new URI
* once received, do a best effort to clean up the old repository

In short, Krill performs a sanity check that the new repository can be used,
and then tries to migrate there in a way that will not lead to invalidating
any currently signed objects.

To start a migration you can use the following.

.. code-block:: text

  $ krillc repo update rfc8183 [file]

API Call: :krill_api_ca_post:`POST /v1/cas/ca/repo <cas~1{ca_handle}~1repo>`

If no file is specified the CLI will try to read the XML from STDIN.

Note that if you were using an embedded repository, and you instruct your CA
to connect to the embedded repository, but set up as a *remote*, then you will
find that you have no more published objects - because.. Krill tries to clean
up the old repository, and we assume that you would not try to use an embedded
server over the RFC 8181 protocol.

But, suppose that you did, you would now see this:

.. code-block:: text

  $ krillc repo show
  Repository Details:
    type:        remote
    service uri: https://localhost:3000/rfc8181/ca
    base_uri:    rsync://localhost/repo/ca/
    rpki_notify: https://localhost:3000/rrdp/notification.xml

  Currently published:
    <nothing>

But no worries.. this can be fixed.

First, you may want to migrate back to using the embedded repository without
the RFC 8181 protocol overhead:

.. code-block:: text

  $ krillc repo update embedded

But this does not solve your problem just yet. Or well, it will re-publish
everything under the new embedded repository, but then it will clean up the
'old' repository which happens to be the same one in this corner case.

The solution is 're-syncing' as described in the following section.


Re-syncing CAs with Repository
------------------------------

If your CAs have somehow become out of sync with their repository, then they
will automatically re-sync whenever there is an update like a renewal of
manifest and crl (every 8 hours), or whenever ROAs are changed. However, you
can force that *all* Krill CAs re-sync using the following.

.. code-block:: text

  $ krillc bulk sync

API Call: :krill_api_ca_post:`POST /v1/cas/resync_all <cas~1resync_all>`
