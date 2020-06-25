.. _doc_krill_cli:
.. highlight:: none

Using the CLI
=============

Introduction
------------

Every function of Krill can be controlled from the command line interface (CLI).
The Krill CLI is a wrapper around the :ref:`Krill API<doc_krill_using_api>`
which is based on JSON over HTTPS.

Note that you can use the CLI from another machine, but then you will need to
set up a proxy server in front of Krill and make sure that it has a real TLS
certificate.

To use the CLI you need to invoke :command:`krillc` followed by one or more
subcommands, and some arguments. Help is built-in:

.. code-block:: bash

   $ :ref:`krillc help<cmd_krillc_help>` [subcommand..]

   USAGE:
       krillc <subcommand..> [FLAGS] [OPTIONS]

   FLAGS:
           --api        Only show the API call and exit. Or set env: KRILL_CLI_API=1
       -h, --help       Prints help information
       -V, --version    Prints version information

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -f, --format <type>     Report format: none\|json\|text (default) \|xml. Or set env: KRILL_CLI_FORMAT
       -s, --server <URI>      The full URI to the krill server. Or set env: KRILL_CLI_SERVER
       -t, --token <string>    The secret token for the krill server. Or set env: KRILL_CLI_TOKEN

Setting Defaults
""""""""""""""""

As noted in the OPTIONS help text above you can set default values via
environment variables for the most common arguments that need to be supplied to
``krillc`` subcommands. When setting environment variables note the following
requirements:

  - ``KRILL_CLI_SERVER`` must be in the form ``https://<host:port>/``.
  - ``KRILL_CLI_MY_CA`` must consist only of alphanumeric characters, dashes and
    underscores, i.e. ``a-zA-Z0-9_``.

For example:

.. code-block:: bash

   export KRILL_CLI_TOKEN="correct-horse-battery-staple"
   export KRILL_CLI_MY_CA="Acme-Corp-Intl"

If you do use the command line argument equivalents, you will override whatever
value you set in the ENV. Krill will give you a friendly error message if you
did not set the applicable ENV variable, and don't include the command line
argument equivalent.

Diagnosing Problems
"""""""""""""""""""

You can show the history of all the things that happened to your CA using the
:ref:`krillc history<cmd_krillc_history>` command.

Reference
---------

The reference below documents the available ``krillc`` subcommands. Flags and
options that are the same for most subcommands are omitted in this section in
order to focus on the most important aspects of each subcommand.

*Tip*: Click on a subcommand name to jump to the help for that subcommand.

.. parsed-literal::

   USAGE:
       krillc [SUBCOMMAND]

   SUBCOMMANDS:
       :ref:`action<cmd_krillc_action>`        Show details for a specific CA action.
       :ref:`add<cmd_krillc_add>`           Add a new CA.
       :ref:`bulk<cmd_krillc_bulk>`          Manually trigger refresh/republish/resync for all cas
       :ref:`children<cmd_krillc_children>`      Manage children for a CA in Krill.
       :ref:`config<cmd_krillc_config>`        Creates a configuration file for krill and prints it to STDOUT.
       :ref:`health<cmd_krillc_health>`        Perform an authenticated health check
       :ref:`help<cmd_krillc_help>`          Prints this message or the help of the given subcommand(s)
       :ref:`history<cmd_krillc_history>`       Show full history of a CA.
       :ref:`info<cmd_krillc_info>`          Show server info
       :ref:`issues<cmd_krillc_issues>`        Show issues for CAs.
       :ref:`keyroll<cmd_krillc_keyroll>`       Perform a manual key-roll in Krill.
       :ref:`list<cmd_krillc_list>`          List the current CAs.
       :ref:`parents<cmd_krillc_parents>`       Manage parents for this CA.
       :ref:`publishers<cmd_krillc_publishers>`    Manage publishers in Krill.
       :ref:`repo<cmd_krillc_repo>`          Manage the repository for your CA.
       :ref:`roas<cmd_krillc_roas>`          Manage ROAs for your CA.
       :ref:`show<cmd_krillc_show>`          Show details of a CA.

.. _cmd_krillc_action:

krillc action
"""""""""""""

Show details for a specific historic CA action.

.. parsed-literal::

   USAGE:
       krillc action [FLAGS] [OPTIONS] --key <action key string>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --key <action key string>    The action key (as shown in the history).

....

.. _cmd_krillc_add:

krillc add
""""""""""

API Call: :krill_api:`POST /v1/cas <add_ca>`

Adds a new CA.

When adding a CA you need to choose a handle, essentially just a name. The term
"handle" comes from :RFC:`8183` and is used in the communication protocol
between parent and child CAs, as well as CAs and publication servers.

The handle you select is not published in the RPKI but used as identification to
parent and child CAs you interact with. You should choose a handle that helps
others recognise your organisation. Once set, the handle cannot be be changed
as it would interfere with the communication between parent and child CAs, as
well as the publication repository.

When a CA has been added, it is registered to publish locally in the Krill
instance where it exists, but other than that it has no configuration yet. In
order to do anything useful with a CA you will first have to add at least one
parent to it, followed by some Route Origin Authorisations and/or child CAs.

.. parsed-literal::

   USAGE:
       krillc add [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

.. note:: The CA name may consist of alphanumeric characters, dashes and
          underscores, i.e. ``a-zA-Z0-9_``.

....

.. _cmd_krillc_bulk:

krillc bulk
"""""""""""

Manually trigger refresh/republish/resync for all CAs.

.. parsed-literal::

   USAGE:
       krillc bulk [SUBCOMMAND]

   SUBCOMMANDS:
       help       Prints this message or the help of the given subcommand(s)
       :ref:`publish<cmd_krillc_bulk_publish>`    Force that all CAs create new objects if needed (in which case they will also sync)
       :ref:`refresh<cmd_krillc_bulk_refresh>`    Force that all CAs ask their parents for updated certificates
       :ref:`sync<cmd_krillc_bulk_sync>`       Force that all CAs sync with their repo server

.. _cmd_krillc_bulk_publish:

krillc bulk publish
^^^^^^^^^^^^^^^^^^^

Force that all CAs create new objects if needed (in which case they will also sync).

.. parsed-literal::

   USAGE:
       krillc bulk publish [FLAGS] [OPTIONS]

.. _cmd_krillc_bulk_refresh:

krillc bulk refresh
^^^^^^^^^^^^^^^^^^^

Force that all CAs ask their parents for updated certificates.

.. parsed-literal::

   USAGE:
       krillc bulk refresh [FLAGS] [OPTIONS]

.. _cmd_krillc_bulk_sync:

krillc bulk sync
^^^^^^^^^^^^^^^^

API Call: :krill_api:`POST /v1/bulk/cas/sync/repo <resync_all_cas>`

Force that all CAs sync with their repo server.

If your CAs have somehow become out of sync with their repository, then they
will automatically re-sync whenever there is an update like a renewal of
manifest and crl (every 8 hours), or whenever ROAs are changed. However, you
can force that *all* Krill CAs re-sync with this command.

.. parsed-literal::

   USAGE:
       krillc bulk sync [FLAGS] [OPTIONS]

....

.. _cmd_krillc_children:

krillc children
"""""""""""""""

Manage children for a CA in Krill.

.. parsed-literal::

   USAGE:
       krillc children [SUBCOMMAND]

   SUBCOMMANDS:
       :ref:`add<cmd_krillc_children_add>`         Add a child to a CA.
       help        Prints this message or the help of the given subcommand(s)
       :ref:`info<cmd_krillc_children_info>`        Show info for a child (id and resources).
       :ref:`remove<cmd_krillc_children_remove>`      Remove an existing child from a CA.
       :ref:`response<cmd_krillc_children_response>`    Get the RFC8183 response for a child.
       :ref:`update<cmd_krillc_children_update>`      Update an existing child of a CA.

.. _cmd_krillc_children_add:

krillc children add
^^^^^^^^^^^^^^^^^^^

API Call: See: :krill_api:`POST /v1/cas/{parent_ca_handle}/children <add_child_ca>`

Add a child to a CA.

To add a child, you will need to:
  1. Choose a unique local name (handle) that the parent will use for the child
  2. Choose initial resources (asn, ipv4, ipv6)
  3. Present the child's :rfc:`8183` request

The default response is the :rfc:`8183` parent response XML file. Or, if you set
``--format json`` you will get the plain API response.

If you need the response again, you can use the
:ref:`krillc children response<cmd_krillc_children_response>` command.

.. parsed-literal::

  USAGE:
      krillc children add [FLAGS] [OPTIONS] --child <name> --request <<XML file>>

  FLAGS:
          --api        Only show the API call and exit. Or set env: KRILL_CLI_API=1
      -h, --help       Prints help information
      -V, --version    Prints version information

  OPTIONS:
      -a, --asn <AS resources>       The delegated AS resources: e.g. AS1, AS3-4
      -c, --ca <name>                The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
          --child <name>             The name of the child CA you wish to control.
      -f, --format <type>            Report format: none|json|text (default). Or set env: KRILL_CLI_FORMAT
      -4, --ipv4 <IPv4 resources>    The delegated IPv4 resources: e.g. 192.168.0.0/16
      -6, --ipv6 <IPv6 resources>    The delegated IPv6 resources: e.g. 2001:db8::/32
      -r, --request <<XML file>>     The location of the RFC8183 Child Request XML file.

.. _cmd_krillc_children_info:

krillc children info
^^^^^^^^^^^^^^^^^^^^

Show info for a child (id and resources).

.. parsed-literal::

   USAGE:
       krillc children info [FLAGS] [OPTIONS] --child <name>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --child <name>      The name of the child CA you wish to control.

.. _cmd_krillc_children_remove:

krillc children remove
^^^^^^^^^^^^^^^^^^^^^^

Remove an existing child from a CA.

.. parsed-literal::

   USAGE:
       krillc children remove [FLAGS] [OPTIONS] --child <name>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --child <name>      The name of the child CA you wish to control.

.. _cmd_krillc_children_response:

krillc children response
^^^^^^^^^^^^^^^^^^^^^^^^

API Call: :krill_api:`GET /v1/cas/{parent_ca_handle}/children/{child_ca_handle}/contact <get_child_ca_parent_contact>`

Get the RFC8183 response for a child.

.. parsed-literal::

   USAGE:
       krillc children response [FLAGS] [OPTIONS] --child <name>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --child <name>      The name of the child CA you wish to control.

.. _cmd_krillc_children_updatE:

krillc children update
^^^^^^^^^^^^^^^^^^^^^^

Update an existing child of a CA.

.. parsed-literal::

   USAGE:
       krillc children update [FLAGS] [OPTIONS] --child <name>

   OPTIONS:
       -a, --asn <AS resources>                  The delegated AS resources: e.g. AS1, AS3-4
       -c, --ca <name>                           The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --child <name>                        The name of the child CA you wish to control.
           --idcert <DER encoded certificate>    The child's updated ID certificate
       -4, --ipv4 <IPv4 resources>               The delegated IPv4 resources: e.g. 192.168.0.0/16
       -6, --ipv6 <IPv6 resources>               The delegated IPv6 resources: e.g. 2001:db8::/32

....

.. _cmd_krillc_config:

krillc config
"""""""""""""

Creates a configuration file for krill and prints it to STDOUT.

.. parsed-literal::

   USAGE:
       krillc config [SUBCOMMAND]

   SUBCOMMANDS:
       help      Prints this message or the help of the given subcommand(s)
       :ref:`repo<cmd_krillc_config_repo>`      Use a self-hosted repository (not recommended)
       :ref:`simple<cmd_krillc_config_simple>`    Use a 3rd party repository for publishing

.. _cmd_krillc_config_simple:

krillc config simple
^^^^^^^^^^^^^^^^^^^^

Creates a configuration file that configures Krill to be used with external
repositories.

.. parsed-literal::

    USAGE:
        krillc config simple [FLAGS] [OPTIONS]

    OPTIONS:
        -d, --data <path>       Override the default path (./data/) for the data directory (must end with slash).
        -l, --logfile <path>    Override the default path (./krill.log) for the log file.

.. _cmd_krillc_config_repo:

krillc config repo
^^^^^^^^^^^^^^^^^^

Creates a configuration file that enables a self-hosted repository within Krill
that CAs can be configured to publish to instead of publishing to an external
repository.

.. Warning:: Running your own repository service is not recommended. For more
             information about using the self-hosted repository see
             :ref:`doc_krill_publication_server`.

.. parsed-literal::

    USAGE:
        krillc config repo [FLAGS] [OPTIONS] --rrdp <uri> --rsync <uri>

    OPTIONS:
        -d, --data <path>       Override the default path (./data/) for the data directory (must end with s
        lash).
        -l, --logfile <path>    Override the default path (./krill.log) for the log file.
            --rrdp <uri>        Specify the base https URI for your RRDP (excluding notify.xml), must end with '/'
            --rsync <uri>       Specify the base rsync URI for your repository. must end with '/'.

....

.. _cmd_krillc_health:

krillc health
"""""""""""""

Perform an authenticated health check. Verifies that the specified Krill server
can be connected to, is able to verify the specified token and is, at least thus
far, healthy.

Can be used in automation scripts by checking the exit code:

+-----------+------------------------------------------------------------------+
| Exit Code | Meaning                                                          |
+===========+==================================================================+
| 0         | the Krill server appears to be healthy.                          |
+-----------+------------------------------------------------------------------+
| non-zero  | incorrect server URI, token, connection failure or server error. |
+-----------+------------------------------------------------------------------+

....

.. _cmd_krillc_help:

krillc help
"""""""""""

Prints the version of ``krillc`` and the complete list of possible subcommands
with a short explanatory text for each one.

....

.. _cmd_krillc_history:

krillc history
""""""""""""""

Show full history of a CA. Using this command you can show the history of all
the things that happened to your CA.

.. parsed-literal::

   USAGE:
       krillc history [FLAGS] [OPTIONS]

   FLAGS:
           --full       Show history including publication.

   OPTIONS:
           --after <<RFC 3339 DateTime>>     Show commands issued after date/time in RFC 3339 format, e.g. 2020-04-
                                             09T19:37:02Z
           --before <<RFC 3339 DateTime>>    Show commands issued after date/time in RFC 3339 format, e.g. 2020-04-
                                             09T19:37:02Z
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --offset <<number>>               Number of results to skip
           --rows <<number>>                 Number of rows (max 250)

Example:

.. code-block:: bash

   $ krillc history
   time::command::key::success
   2020-06-07T20:33:21Z::Update repo to server at: https://localhost:3000/rfc8181/ca ::command--1591562001--1--cmd-ca-repo-update::OK
   2020-06-07T20:34:18Z::Add parent 'ripencc' as 'RFC 6492 Parent' ::command--1591562058--2--cmd-ca-parent-add::OK
   2020-06-07T20:34:19Z::Update entitlements under parent 'ripencc': 0 => asn: 0 blocks, v4: 1 blocks, v6: 1 blocks  ::command--1591562059--3--cmd-ca-parent-entitlements::OK
   2020-06-07T20:34:20Z::Update received cert in RC '0', with resources 'asn: 0 blocks, v4: 1 blocks, v6: 1 blocks' ::command--1591562060--4--cmd-ca-rcn-receive::OK
   2020-06-07T20:36:28Z::Update ROAs add: 2 remove: '0' ::command--1591562188--5--cmd-ca-roas-updated::OK

....

.. _cmd_krillc_info:

krillc info
"""""""""""

Show server info. Prints the version of the Krill *server* and the date and time
that it was last started, e.g.:

.. parsed-literal::

   USAGE:
       krillc info [FLAGS] [OPTIONS]

Example:

.. code-block:: bash

   $ krillc info
   Version: 0.7.1
   Started: 2020-05-16T10:21:36+00:00

....

.. _cmd_krillc_issues:

krillc issues
"""""""""""""

Show issues for CAs.

.. parsed-literal::

   USAGE:
       krillc issues [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

....

.. _cmd_krillc_keyroll:

krillc keyroll
""""""""""""""

Perform a manual key-roll in Krill.

.. parsed-literal::

   USAGE:
       krillc keyroll [SUBCOMMAND]

   SUBCOMMANDS:
       :ref:`activate<cmd_krillc_keyroll_activate>`    Finish roll for all keys held by this CA.
       help        Prints this message or the help of the given subcommand(s)
       :ref:`init<cmd_krillc_keyroll_init>`        Initialise roll for all keys held by this CA.

.. _cmd_krillc_keyroll_activate:

krillc keyroll activate
^^^^^^^^^^^^^^^^^^^^^^^

Finish roll for all keys held by this CA.

.. parsed-literal::

   USAGE:
       krillc keyroll activate [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

.. _cmd_krillc_keyroll_init:

krillc keyroll init
^^^^^^^^^^^^^^^^^^^

Initialise roll for all keys held by this CA.

.. parsed-literal::

   USAGE:
       krillc keyroll init [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

....

.. _cmd_krillc_list:

krillc list
"""""""""""

API Call: :krill_api:`GET /v1/cas <list_cas>`

List the current CAs.

.. parsed-literal::

   USAGE:
       krillc list [FLAGS] [OPTIONS]

....

.. _cmd_krillc_parents:

krillc parents
""""""""""""""

Manage parents for this CA.

.. parsed-literal::

   USAGE:
       krillc parents [SUBCOMMAND]

   SUBCOMMANDS:
       :ref:`add<cmd_krillc_parents_add>`        Add a parent to this CA.
       :ref:`contact<cmd_krillc_parents_contact>`    Show contact information for a parent of this CA.
       help       Prints this message or the help of the given subcommand(s)
       :ref:`remove<cmd_krillc_parents_remove>`     Remove an existing parent from this CA.
       :ref:`request<cmd_krillc_parents_request>`    Show RFC8183 Publisher Request XML
       :ref:`update<cmd_krillc_parents_update>`     Update an existing parent of this CA.

.. _cmd_krillc_parents_add:

krillc parents add
^^^^^^^^^^^^^^^^^^

API Call: :krill_api:`POST /v1/cas/ca/parents <add_ca_parent>`

Add a parent to this CA.

.. parsed-literal::

   USAGE:
       krillc parents add [FLAGS] [OPTIONS] --parent <name> --response <<XML file>>

   OPTIONS:
       -c, --ca <name>               The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -p, --parent <name>           The local name by which your ca refers to this parent.
       -r, --response <<XML file>>   The RFC8183 Parent Response XML

Note that you can use any local name for ``--parent``. This is the name that
Krill will show to you. Similarly, Krill will use your local CA name which you
set in the ```KRILL_CLI_MY_CA`` ENV variable. However, the parent response
includes the names (or handles as they are called in the RFC) by which it refers
to itself, and your CA. Krill will make sure that it uses these names in the
communication with the parent. There is no need for these names to be the same.

Note that whichever handle you choose, your CA will use the handles that the
parent response included for itself *and* for your CA in its communication with
this parent. I.e. you may want to inspect the response and use the same handle
for the parent (parent_handle attribute), and do not be surprised or alarmed if
the parent refers to your ca (child_handle attribute) by some seemingly random
name. Some parents do this to ensure unicity.

In case you have multiple parents you may want to refer to them by names that
make sense in your context.

.. _cmd_krillc_parents_contact:

krillc parents contact
^^^^^^^^^^^^^^^^^^^^^^

Show contact information for a parent of this CA.

.. parsed-literal::

   USAGE:
       krillc parents contact [FLAGS] [OPTIONS] --parent <name>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -p, --parent <name>     The local name by which your ca refers to this parent.

.. _cmd_krillc_parents_remove:

krillc parents remove
^^^^^^^^^^^^^^^^^^^^^

Remove an existing parent from this CA.

.. parsed-literal::

   USAGE:
       krillc parents remove [FLAGS] [OPTIONS] --parent <name>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -p, --parent <name>     The local name by which your ca refers to this parent.

.. _cmd_krillc_parents_request:

krillc parents request
^^^^^^^^^^^^^^^^^^^^^^

API Call: :krill_api:`GET /v1/cas/{ca_handle}/child_request.json <get_ca_child_request>`

Show :rfc:`8183` Publisher Request XML for the named CA. This XML is needed when
registering the CA as a child of another CA. For more information see
:ref:`doc_krill_using_ui_parent_setup`.

.. parsed-literal::

   USAGE:
       krillc parents request [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

.. _cmd_krillc_parents_update:

krillc parents update
^^^^^^^^^^^^^^^^^^^^^

Update the known information about existing parent of this CA. Note there is no
good description of this in the RFCs for the moment. You should not need this
option in practice. However, this will allow to replace the parent response for
one of your parents.

.. parsed-literal::

   USAGE:
       krillc parents update [FLAGS] [OPTIONS] --parent <name> --response <<XML file>>

   OPTIONS:
       -c, --ca <name>               The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -p, --parent <name>           The local name by which your ca refers to this parent.
           --response <<XML file>>   The RFC8183 Parent Response XML

....

.. _cmd_krillc_publishers:

krillc publishers
"""""""""""""""""

Manage publishers in Krill.

.. parsed-literal::

   USAGE:
       krillc publishers [SUBCOMMAND]

   SUBCOMMANDS:
       :ref:`add<cmd_krillc_publishers_add>`         Add a publisher.
       help        Prints this message or the help of the given subcommand(s)
       :ref:`list<cmd_krillc_publishers_list>`        List all publishers.
       :ref:`remove<cmd_krillc_publishers_remove>`      Remove a publisher.
       :ref:`response<cmd_krillc_publishers_response>`    Show RFC8183 Repository Response for a publisher.
       :ref:`show<cmd_krillc_publishers_show>`        Show details for a publisher.
       :ref:`stale<cmd_krillc_publishers_stale>`       List all publishers which have not published in a while.
       :ref:`stats<cmd_krillc_publishers_stats>`       Show publication server stats.

.. _cmd_krillc_publishers_add:

krillc publishers add
^^^^^^^^^^^^^^^^^^^^^

Add a publisher. In order to add a publisher you have to get its :rfc:`8183`
Publisher Request XML, and hand it over to the server.

.. parsed-literal::

   USAGE:
       krillc publishers add [FLAGS] [OPTIONS] --request <file>

   OPTIONS:
       -p, --publisher <handle>    Override the publisher handle in the XML.
       -r, --request <file>        RFC8183 Publisher Request XML file containing a certificate (tag is ignored)

.. _cmd_krillc_publishers_list:

krillc publishers list
^^^^^^^^^^^^^^^^^^^^^^

List all publishers under the Publication Server.

.. parsed-literal::

   USAGE:
       krillc publishers list [FLAGS] [OPTIONS]

.. _cmd_krillc_publishers_remove:

krillc publishers remove
^^^^^^^^^^^^^^^^^^^^^^^^

Remove a publisher. If you do, then all of its content will be removed as well
and the publisher will no longer be allowed to publish.

.. Warning:: You can do this without the publisher’s knowledge, nor consent.
             You should check with the publisher whether they no longer need
             your Publication Server (perhaps they migrated to another, or
             disabled their CA).

.. parsed-literal::

   USAGE:
       krillc publishers remove [FLAGS] [OPTIONS] --publisher <handle>

   OPTIONS:
       -p, --publisher <handle>    The handle (name) of the publisher.

.. _cmd_krillc_publishers_response:

krillc publishers response
^^^^^^^^^^^^^^^^^^^^^^^^^^

Show RFC8183 Repository Response for a publisher.

.. parsed-literal::

   USAGE:
       krillc publishers response [FLAGS] [OPTIONS] --publisher <handle>

   OPTIONS:
       -p, --publisher <handle>    The handle (name) of the publisher.

Example:

.. code-blocK:: bash

   $ krillc publishers response --publisher ca
   <repository_response xmlns="http://www.hactrn.net/uris/rpki/rpki-setup/" version="1" publisher_handle="ca" service_uri="https://localhost:3000/rfc8181/ca" sia_base="rsync://localhost/repo/ca/" rrdp_notification_uri="https://localhost:3000/rrdp/notification.xml">
     <repository_bpki_ta> repository server id certificate base64 </repository_bpki_ta>
   </repository_response>

.. _cmd_krillc_publishers_show:

krillc publishers show
^^^^^^^^^^^^^^^^^^^^^^

Show details for a publisher, including the files that they published.

The default text output just shows the handle of the publisher, the hash of its
identity certificate key, and the rsync URI jail under which the publisher is
allowed to publish objects.

The JSON response includes a lot more information, including the files which
were published and the full ID certificate used by the publisher.

.. parsed-literal::

   USAGE:
       krillc publishers show [FLAGS] [OPTIONS] --publisher <handle>

   OPTIONS:
       -p, --publisher <handle>    The handle (name) of the publisher.

.. _cmd_krillc_publishers_stale:

krillc publishers stale
^^^^^^^^^^^^^^^^^^^^^^^

List all publishers which have not published in a while.

.. parsed-literal::

   USAGE:
       krillc publishers stale [FLAGS] [OPTIONS] --seconds <seconds>

   OPTIONS:
           --seconds <seconds>    The number of seconds since last publication.

.. _cmd_krillc_publishers_stats:

krillc publishers stats
^^^^^^^^^^^^^^^^^^^^^^^

Show publication server stats.

.. parsed-literal::

   USAGE:
       krillc publishers stats [FLAGS] [OPTIONS]

....

.. _cmd_krillc_repo:

krillc repo
"""""""""""

Manage the repository for your CA.

.. parsed-literal::

   USAGE:
       krillc repo [SUBCOMMAND]

   SUBCOMMANDS:
       help       Prints this message or the help of the given subcommand(s)
       :ref:`request<cmd_krillc_repo_request>`    Show RFC8183 Publisher Request.
       :ref:`show<cmd_krillc_repo_show>`       Show current repo config.
       :ref:`state<cmd_krillc_repo_state>`      Show current repo state.
       :ref:`update<cmd_krillc_repo_update>`     Change which repository this CA uses.

.. _cmd_krillc_repo_request:

krillc repo request
^^^^^^^^^^^^^^^^^^^

Show the :rfc:`8183` Publisher Request XML for a CA. You will need to hand this
over to your repository so that they can add your CA.

.. parsed-literal::

   USAGE:
       krillc repo request [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

Example:

.. code-block:: bash

   $ krillc repo request
   <publisher_request xmlns="http://www.hactrn.net/uris/rpki/rpki-setup/" version="1" publisher_handle="ca">
     <publisher_bpki_ta>your CA ID cert DER in base64</publisher_bpki_ta>
   </publisher_request>

.. _cmd_krillc_repo_show:

krillc repo show
^^^^^^^^^^^^^^^^

Show the repository configuration for your CA.

.. parsed-literal::

   USAGE:
       krillc repo show [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

Example:

.. code-block:: bash

  $ krillc repo show
  Repository Details:
    type:        remote
    service uri: https://krill-ui-dev.do.nlnetlabs.nl/rfc8181/Acme-Corp-Intl
    base_uri:    rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/
    rpki_notify: https://krill-ui-dev.do.nlnetlabs.nl/rrdp/notification.xml

.. _cmd_krillc_repo_state:

krillc repo state
^^^^^^^^^^^^^^^^^

Show which repository server your CA is using, as well as what is has published
at the location. Krill will issue an actual list query to the repository and
give back the response, or an error in case of issues.

.. parsed-literal::

   USAGE:
       krillc repo state [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA


Example:

.. code-block:: bash

  $ krillc repo state
  Available and publishing objects:
    407cb2f9c72ae0a65badb49c073fc9df79c170336b3c14734e8e2ec9e1c106ca rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/0/5796A18D9A941AB72D78C820C5F0837B1CB30694.mft
    ad2afbf2fddfc0212df4c9b4bc1834b1d9c1a2ec1e982ec08e9cef92aa8ca912 rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/0/31302e302e302e302f32322d3232203d3e203634343936.roa
    79f8fdc1e1c42ee1e029a65011e28415515602b12f6e850799adeb1fc515339b rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/0/5796A18D9A941AB72D78C820C5F0837B1CB30694.crl
    6f7029b9e9a8c96eb1c5c388bd6f1cbcaac6cd24885e666f4cb5a218647beff2 rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/0/31302e302e302e302f32322d3232203d3e203634343937.roa
    f02dbc841c4c321529f8c5b32a2cbe30bfcc08641b044511c5ba0d81ee8fd6ba rsync://krill-ui-dev.do.nlnetlabs.nl/repo/Acme-Corp-Intl/0/31302e302e302e302f32342d3234203d3e203634343936.roa


.. _cmd_krillc_repo_update:

krillc repo update
^^^^^^^^^^^^^^^^^^

Change which repository this CA uses.

You can change which repository server is used by your CA. If you have multiple
CAs you will have to repeat this for each of them.

Changing repositories is actually more complicated than one might think, but
fortunately it's all automated. When you ask Krill to change, the following
steps will be executed:

- check that the new repository can be reached, and this ca is authorised
- regenerate all objects using the URI jail given by the new repository
- publish all objects in the new repository
- request new certificates from (all) parent CA(s) including the new URI
- once received, do a best effort to clean up the old repository

In short, Krill performs a sanity check that the new repository can be used,
and then tries to migrate there in a way that will not lead to invalidating
any currently signed objects.

To start a migration you can use the following.

.. parsed-literal::

   USAGE:
       krillc repo update [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
       -r, --response <file>   The location of the RFC8183 Publisher Response XML file. Defaults to reading from STDIN


.. _cmd_krillc_roas:

krillc roas
"""""""""""

Manage ROAs for your CA.

Krill lets users create Route Origin Authorisations (ROAs), the signed objects
that state which Autonomous System (AS) is authorised to originate one of your
prefixes, along with the maximum prefix length it may have.

.. parsed-literal::

   USAGE:
       krillc roas [SUBCOMMAND]

   SUBCOMMANDS:
       help      Prints this message or the help of the given subcommand(s)
       :ref:`list<cmd_krillc_roas_list>`      Show current authorizations.
       :ref:`update<cmd_krillc_roas_update>`    Update authorizations.

.. _cmd_krillc_roas_list:

krillc roas list
^^^^^^^^^^^^^^^^

Show current authorizations.

.. parsed-literal::

   USAGE:
       krillc roas list [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

Example:

You can list ROAs in the following way:

.. code-block:: bash

   $ krillc roas list
   192.0.2.0/24 => 64496
   2001:db8::/32-48 => 64496

.. _cmd_krillc_roas_update:

krillc roas update
^^^^^^^^^^^^^^^^^^

API Call: :krill_api:`POST /v1/cas/ca/routes <update_route_authorizations>`

Update authorizations.

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

   # Some comment
     # Indented comment

   A: 10.0.0.0/24 => 64496
   A: 10.1.0.0/16-20 => 64496   # Add prefix with max length
   R: 10.0.3.0/24 => 64496      # Remove existing authorization

.. parsed-literal::

   USAGE:
       krillc roas update [FLAGS] [OPTIONS] --delta <<file>>

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA
           --delta <<file>>    Provide a delta file using the following format:
                               # Some comment
                                 # Indented comment

                               A: 192.168.0.0/16 => 64496 # inline comment
                               A: 192.168.1.0/24 => 64496
                               R: 192.168.3.0/24 => 64496

Example:

.. code-block:: bash

   $ krillc roas update --delta ./roas.txt

....

.. _cmd_krillc_show:

krillc show
"""""""""""

API Call: :krill_api:`GET /v1/cas/{ca_handle} <get_ca>`

Show details of a CA.

.. parsed-literal::

   USAGE:
       krillc show [FLAGS] [OPTIONS]

   OPTIONS:
       -c, --ca <name>         The name of the CA you wish to control. Or set env: KRILL_CLI_MY_CA

Example:

.. code-block:: bash

   $ krillc show --ca ca
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
