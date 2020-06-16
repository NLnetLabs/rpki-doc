.. _doc_krill_testing:

Running a Test Environment
==========================

If you want to get operational experience with Krill before before configuring a
production parent, you can run with an embedded Trust Anchor (TA) which you can
give any address space you want. You can generate your own Trust Anchor for it,
which can be added to your Relying Party software in order to validate the
objects you have published locally.

.. Note:: The Krill TA is intended for test purposes only and as such there
          are some limitations when using it:

          - The Krill Trust Anchor Locator (TAL) points to the TA certificate
            using an ``https://`` URI. It does not include an ``rsync://`` URI.
          - Only Relying Party software which supports such an HTTPS TAL can be
            used with the Krill TA.
          - When enabling the embedded TA ``use_ta = true`` the Krill daemon
            assumes that you also set ``repo_enabled = true`` to enable the
            embedded repository. **Tip:** The ``krillc config repo`` command
            does this for you.
          - The TA claims ownership of all possible ASNs, IPv4 addresses and
            IPv6 addresses. It is not currently possible to restrict this to a
            subset of these resources.

Setting up the Configuration
----------------------------

For testing we will assume that you will run your own Krill repository inside a
single Krill instance, using 'localhost' in the repository URIs. You have to set
the following environment variable to re-assure Krill that you are running a
test environment, or it will refuse the use of 'localhost':

.. code-block:: bash

   $ export KRILL_TEST="true"

For convenience you may wish to set the following variables, so that you don't
have to repeat command line arguments for these:

.. code-block:: bash

   $ export KRILL_CLI_SERVER="https://localhost:3000/"
   $ export KRILL_CLI_TOKEN="correct-horse-battery-staple"
   $ export KRILL_CLI_MY_CA="ca"

.. Note:: Replace *"correct-horse-battery-staple"* with a token of your own
          choosing! If you don't the UI will kindly remind you that
          "You should not get your passwords from https://xkcd.com/936/".

You can now generate a krill configuration file using the following command:

.. parsed-literal::

   $ :ref:`krillc config repo<cmd_krillc_config_repo>` \\
      --token $KRILL_CLI_TOKEN \\
      --rrdp https://localhost:3000/rrdp/ \\
      --rsync rsync://localhost/repo/ > /tmp/krill.conf \\
      --data /tmp/krill_data

Enable the Embedded Trust Anchor (TA)
-------------------------------------

To run Krill in test mode you can set "use_ta" to "true" in your
:file:`krill.conf`, or use an environment variable:

.. code-block:: bash

   $ export KRILL_USE_TA="true"
   $ krill -c /tmp/krill.conf &

Verify that the TA now exists:

.. parsed-literal::

  $ :ref:`krillc list<cmd_krillc_list>`
  ta

Use the following to show more details of the embedded TA:

.. parsed-literal::

   $ :ref:`krillc show<cmd_krillc_show>` --ca ta
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

Example Usage with a TA
-----------------------

In this example we show you how to create a CA, register it with the embedded
repository and as a child of the TA, and how to publish ROAs.

Create a CA
"""""""""""

.. parsed-literal::

  $ :ref:`krillc add<cmd_krillc_add>`

Verify that now both TA and CA exist:

.. parsed-literal::

  $ :ref:`krillc list<cmd_krillc_list>`
  ta
  ca

Register the CA with a repository
"""""""""""""""""""""""""""""""""

You can do the CA part of this :ref:`using the UI<doc_krill_using_ui_repository_setup>`.

But, if your CAs and your test publication server are all running in the same
Krill instance you can quickly do the full set up using the CLI.

.. parsed-literal::

  $ :ref:`krillc repo request<cmd_krillc_repo_request>` > publisher_request.xml

  $ :ref:`krillc publishers add<cmd_krillc_publishers_add>` \\
     --publisher $KRILL_CLI_MY_CA \\
     --rfc8183 publisher_request.xml > repository_response.xml

  $ :ref:`krillc repo update remote<cmd_krillc_repo_update_remote>` --rfc8183 repository_response.xml

Use the TA as the Parent of the CA
""""""""""""""""""""""""""""""""""

When using an embedded TA for testing then you will first need to add your
new CA "ca" to it. The steps below are not specific to the TA, the same steps
must be taken when :ref:`registering any CA with a parent CA <doc_krill_using_ui_parent_setup>`.

Note: In this example we register with the TA as if it were remote rather than
embedded. This is slightly less efficient, but it's the same as what you would
need to delegate from your CA to remote CAs.

Step 1: Obtain the RFC 8183 request XML
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. parsed-literal::

  $ :ref:`krillc parents request<cmd_krillc_parents_request>` > myid.xml

Step 2: Add the CA as a Child of the TA
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In this example we need to override the ENV variable in order to refer to the TA
and not the CA, and we need to indicate that we want to add this child to the CA
"ta". The following command will add the child, and the :rfc:`8183` XML from the
"ta":

.. parsed-literal::

  $ :ref:`krillc children add remote<cmd_krillc_children_add_remote>` --ca ta \\
      --child ca \\
      --ipv4 "10.0.0.0/8" --ipv6 "2001:DB8::/32" \\
      --rfc8183 myid.xml > parent-res.xml

If you need the response again, you can ask the "ta" again:

.. parsed-literal::

  $ :ref:`krillc children response<cmd_krillc_children_response>` --ca "ta" --child "ca"

Step 3: Add the TA as the Parent of the CA
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. parsed-literal::

  $ :ref:`krillc parents add remote<cmd_krillc_parents_add_remote>` --parent myta --rfc8183 ./parent-res.xml

Now you should see that your "child" is certified:

.. parsed-literal::

  $ :ref:`krillc show<cmd_krillc_show>`
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
  Handle: myta Kind: RFC 6492 Parent

  Resource Class: 0
  Parent: myta
  State: active    Resources:
      ASNs:
      IPv4: 10.0.0.0/8
      IPv6: 2001:db8::/32
  Current objects:
    553A7C2E751CA0B04B49CB72E30EB5684F861987.crl
    553A7C2E751CA0B04B49CB72E30EB5684F861987.mft

  Children:
  <none>

Add and List ROAs
"""""""""""""""""

.. parsed-literal::

   $ cat >./roas.txt <<EOF
   A: 10.0.0.0/24 => 64496
   A: 10.1.0.0/16-20 => 64496
   EOF

   $ :ref:`krillc roas update<cmd_krillc_roas_update>` --delta ./roas.txt

   $ :ref:`krillc roas list<cmd_krillc_roas_list>`
   10.1.0.0/16-20 => 64496
   10.0.0.0/24 => 64496

Review your CA History
""""""""""""""""""""""

.. parsed-literal::

   $ :ref:`krillc history<cmd_krillc_history>`
   time::command::key::success
   2020-06-07T20:33:21Z::Update repo to server at: https://localhost:3000/rfc8181/ca ::command--1591562001--1--cmd-ca-repo-update::OK
   2020-06-07T20:34:18Z::Add parent 'myta' as 'RFC 6492 Parent' ::command--1591562058--2--cmd-ca-parent-add::OK
   2020-06-07T20:34:19Z::Update entitlements under parent 'myta': 0 => asn: 0 blocks, v4: 1 blocks, v6: 1 blocks  ::command--1591562059--3--cmd-ca-parent-entitlements::OK
   2020-06-07T20:34:20Z::Update received cert in RC '0', with resources 'asn: 0 blocks, v4: 1 blocks, v6: 1 blocks' ::command--1591562060--4--cmd-ca-rcn-receive::OK
   2020-06-07T20:36:28Z::Update ROAs add: 2 remove: '0' ::command--1591562188--5--cmd-ca-roas-updated::OK

Using Routinator with the Test TA
"""""""""""""""""""""""""""""""""

While there are many :ref:`Relying Party tools<relying_party_software>`, when
testing with the Krill TA as noted above you will need an RP that supports a TAL
file that contains an HTTPS URI.

One such RP is :ref:`NLnet Labs Routinator<doc_routinator>`. However, before you
can use Routinator with Krill you will need to either setup Krill on a proper
domain name with a matching TLS certificate issued by a trusted authority, or
issue your own certificate and force Routinator to trust it.

Issue Own TLS Certificate
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   $ mkdir /tmp/own_cert
   $ cd /tmp/own_cert
   $ ISSUER="/C=NL/L=Amsterdam/O=Your Organisation Name"
   $ SUBJECT="/C=NL/L=Amsterdam/O=Your Organisation Name/CN=localhost"
   $ SAN="DNS:localhost"
   $ openssl req -new \
   $         -newkey rsa:4096 -keyout issuer.key \
   $         -x509 -out issuer.crt \
   $         -days 365 -nodes -subj "$ISSUER"
   $ openssl req -new -out subject.csr \
   $         -newkey rsa:4096 -keyout subject.key \
   $         -days 365 -nodes -subj "$SUBJECT"
   $ echo "subjectAltName=$SAN" > subject.ext
   $ openssl x509 \
   $         -in subject.csr -req -out subject.crt -extfile subject.ext \
   $         -CA issuer.crt -CAkey issuer.key -CAcreateserial \
   $         -days 365

Reconfigure Krill
^^^^^^^^^^^^^^^^^

.. code-block:: bash

   $ kill $(cat /tmp/krill_data/krill.pid)
   $ cp /tmp/own_cert/subject.crt /tmp/krill_data/ssl/cert.pem
   $ cp /tmp/own_cert/subject.key /tmp/krill_data/ssl/key.pem
   $ export KRILL_TEST="true"
   $ export KRILL_USE_TA="true"
   $ krill -c /tmp/krill.conf &

Initialize Routinator
^^^^^^^^^^^^^^^^^^^^^

To point Routinator at our test Krill TA we must download the TAL and store it
where Routinator can find it. Also, to ensure that we don't interfere with any
existing Routinator cache on your computer let's create a temporary cache
directory for Routinator to use.

.. code-block:: bash

   $ mkdir -p /tmp/routinator/{tals,rpki-cache}
   $ wget -q --no-check-certificate -O /tmp/routinator/tals/ta.tal \
         https://localhost:3000/ta/ta.tal

Run Routinator
^^^^^^^^^^^^^^

To successfully use Routinator with the Krill TA we must specify the following
command line options:

+-------------------------+---------------------------------------------------+
| Option                  | Explanation                                       |
+=========================+===================================================+
| ``repository-dir``      | Location of the Routinator cache directory.       |
+-------------------------+---------------------------------------------------+
| ``tal-dir``             | Location of the Krill TA file                     |
+-------------------------+---------------------------------------------------+
| ``rrdp-root-cert``      | Location of the certificate of the authority that |
|                         | issued the Krill TLS certificate                  |
+-------------------------+---------------------------------------------------+
| ``allow-dubious-hosts`` | Do **NOT** skip the Krill localhost repository    |
+-------------------------+---------------------------------------------------+

The full command to invoke Routinator and the output showing our test ROAs is
then:

.. code-block:: bash

   $ routinator \
        --repository-dir=/tmp/routinator/rpki-cache \
        --tal-dir=/tmp/routinator/tals \
        --rrdp-root-cert=/tmp/own_cert/issuer.crt \
        --allow-dubios-hosts \
        vrps
   ASN,IP Prefix,Max Length,Trust Anchor
   AS64496,10.0.0.0/24,24,ta
   AS64496,10.1.0.0/16,20,ta
