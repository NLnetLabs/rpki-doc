.. _doc_krill_get_started:

Get Started with Krill
======================

Before you can start managing your own Route Origin Authorisations (ROAs) you
need to do a one time setup where you:

- create your Certificate Authority (CA)
- connect to a Publication Server
- connect to a Parent CA (typically a Regional or National Internet Registry)

This can be easily achieved using the user interface. Connecting to the
Publication Server and Parent CA is done by exchanging a couple of XML files.
After this initial setup, and you can simply :ref:`manage your
ROAs<doc_krill_manage_roas>`.

If you just want to try out Krill (or a new version) you can use the
`testbed environment <https://blog.nlnetlabs.nl/testing----123-delegated-rpki/>`_
provided by NLnet Labs for this.

If you are using the defaults you can access the user interface in a browser on
the server running Krill at ``https://localhost:3000``. By default, Krill
generates a self-signed TLS certificate, so you will have to accept the security
warning that your browser will give you.

If you want to access the UI, or use the CLI, from another computer, you can
either :ref:`set up a reverse proxy<proxy_and_https>` on your server
running Krill, or set up local port forwarding with SSH, for example:

.. code-block:: bash

  ssh -L 3000:localhost:3000 user@krillserver.example.net

Here we will guide you through the set up process using the UI, but we will also
link to the relevant subcommands of the :ref:`command line interface
(CLI)<doc_krill_cli>`

Login
-----

The login will ask you to enter the secret token you configured for Krill.

.. figure:: img/krill-ui-enter-password.png
    :align: center
    :width: 100%
    :alt: Password screen

    Enter your secret token to access Krill

If you are using the CLI you will need to specify the token using the
``--token`` option. Because the CLI does not have a session, you will need to
specify this for each command. Alternatively, you can set the the
``KRILL_CLI_TOKEN`` environment variable.

Create your Certificate Authority
---------------------------------

Next, you will see the Welcome screen where you can create your Certificate
Authority (CA). It will be used to configure delegated RPKI with one or multiple
parent CAs, usually your Regional or National Internet Registry.

The handle you select is not published in the RPKI but used as identification to
parent and child CAs you interact with. Please choose a handle that helps others
recognise your organisation. Once set, the handle cannot be changed.

.. figure:: img/krill-ui-welome.png
    :align: center
    :width: 100%
    :alt: Welcome screen

    Enter a handle for your Certification Authority

If you are using the CLI you can create your CA using the subcommand
:ref:`krillc add<cmd_krillc_add>`.

.. _member_portals:

RIR and NIR Interactions
------------------------

If you hold resources in one or more RIR or NIR regions, you will need to have
access to the respective member portals and the permission to configure
delegated RPKI.

  :AFRINIC:
       https://my.afrinic.net

  :APNIC:
       https://myapnic.net

  :ARIN:
       https://account.arin.net

  :LACNIC:
       https://milacnic.lacnic.net

  :RIPE NCC:
       https://my.ripe.net

Most RIRs have a few considerations to keep in mind.

AFRINIC
"""""""

AFRINIC have delegated RPKI available in their test environment, but it’s not
operational yet. Work to bring it to production is planned for 2021.

APNIC
"""""

If you are already using the hosted RPKI service provided by APNIC and you would
like to switch to delegated RPKI, there is currently no option for this with
MyAPNIC. Please open a ticket with the APNIC help desk to resolve this.

Please note that APNIC offers RPKI publication as a service upon request. It is
highly recommended to make use of this, as it relieves you of the need to run a
highly available repository yourself.

LACNIC
""""""

Although LACNIC offers delegated RPKI, it is not possible to configure this in
their member portal yet. While the procedures are still being defined, please
open a ticket via hostmaster@lacnic.net to get started.

RIPE NCC
""""""""

When you are a RIPE NCC member who does not have RPKI configured, you will be
presented with a choice if you would like to use hosted or non-hosted RPKI.

.. figure:: img/ripencc-hosted-non-hosted.png
    :align: center
    :width: 100%
    :alt: RIPE NCC RPKI setup screen

    RIPE NCC RPKI setup screen

If you want to set up delegated RPKI with Krill, you will have to choose
non-hosted. If you are already using the hosted service and you would like to
switch, then there is currently no option for that in the RIPE NCC portal.

Make a note of the ROAs you created and then send an email to rpki@ripe.net
requesting your hosted CA to be deleted, making sure to mention your
registration id. After deletion, you will land on the setup screen from where
you can choose non-hosted RPKI.

.. _doc_krill_using_ui_repository_setup:

Hosted Publication Server
-------------------------

Your RIR or NIR may also provide an RPKI publication server. You are free to
publish your certificate and ROAs anywhere you like, so a third party may
provide an RPKI publication server as well. Using an RPKI publication server
relieves you of the responsibility to keep a public rsync and web server running
at all times to make your certificate and ROAs available to the world.

Of the five RIRs, only APNIC currently offers RPKI publication as a service for
their members, upon request. Most other RIRs have it on their roadmap. NIC.br,
the Brazilian NIR, provides an RPKI repository server for their members as well.
This means that in most cases you will have to publish your certificate and ROAs
yourself, as described in the :ref:`doc_krill_publication_server` section.

Repository Setup
----------------

Before Krill can request a certificate from a parent CA, it will need to know
where it will publish. You can add a parent before configuring a repository for
your CA, but in that case Krill will postpone requesting a certificate until
you have done so.

In order to register your CA as a publisher, you will need to copy the
:RFC:`8183` Publisher Request XML and supply it to your Publication Server. You
can retrieve this file with the CLI subcommand :ref:`krillc repo
request<cmd_krillc_repo_request>`, or you can simply use the UI:

.. figure:: img/krill-ui-publisher-request.png
    :align: center
    :width: 100%
    :alt: Publisher request

    Copy the publisher request XML or download the file

Your publication server provider will give you a repository response XML. You
can use the CLI subcommand :ref:`krillc repo update<cmd_krillc_repo_update>` to
tell add this configuration to your CA, or you can simply use the UI:

.. figure:: img/krill-ui-repository-response.png
    :align: center
    :width: 100%
    :alt: Repository response

    Paste or upload the repository response XML

.. _doc_krill_using_ui_parent_setup:

Parent Setup
------------

After successfully configuring the repository, the next step is to configure
your parent CA. You will need to present your CA's :RFC:`8183` child request XML
file to your parent. You can get this file using the CLI subcommand
:ref:`krillc parents request<cmd_krillc_parents_request>`, or you can simply
use the UI:

.. figure:: img/krill-ui-child-request.png
    :align: center
    :width: 100%
    :alt: Child request

    Copy the child request XML or download the file

Your RIR or NIR will provide you with a parent response XML. You can use the
CLI subcommand :ref:`krillc parents add<cmd_krillc_parents_add>` for this, or
you can simply paste or upload it using the UI:

.. figure:: img/krill-ui-parent-response.png
    :align: center
    :width: 100%
    :alt: Parent response

    Paste or upload the parent response XML
    
After a few moments your parent will process your entitled resources and you
will see them appearing on your certificate which is visible in the ROAs tab. 
Now you can start creating ROAs.
