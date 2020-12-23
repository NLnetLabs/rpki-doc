.. _doc_krill_publication_server:

Running a Publication Server
============================

.. Important:: It is highly recommended to use an RPKI publication server
               provided by your parent CA, if available. This relieves you of
               the responsibility to keep a public rsync and web server
               available at all times.

               **Both nic.br and APNIC provide publication as a service to their
               members.**

Why run your own?
-----------------

If your parent CA does not offer publication as a service, then you will need
to run your own server. But another reason why you may want to run your own
Publication Server is that it will allow you to delegate your CA's resources
to your own child CAs - e.g. for business units - and allow your children to
publish at your central repository as well.

In this model you will need to set up your CA as a :ref:`child under your parent<doc_krill_using_ui_parent_setup>`,
and :ref:`set it up to publish<doc_krill_using_ui_repository_setup>` at your
local Publication Server:

.. figure:: img/parent-child-repo.*
    :align: center
    :width: 100%
    :alt: Running your own publication server

    Running a publication server for yourself and your children


Install
-------

If you need to run your own Publication Server, then you can use the separate
``krillpubd`` binary for the server, and the ``krillpubc`` binary as its command line
interface (CLI). Both additional binaries are built when you :ref:`install Krill<doc_krill_install_and_run>`,
but they are only used if you need to run your own Publication Server.

.. Note:: The Krill Publication Server does not have a UI. You will need to
          manage it using the CLI or API instead.


Configure
---------

Your Publication Server can use a very minimal configuration file, similar
in style to the one used by the Krill CA server. You should configure the
following settings:

.. code-block:: bash

  # choose your own secret for the authorization token for the CLI and API
  auth_token =

  data_dir = "/path/to/your/krillpubd/data/"
  pid_file = "/path/to/your/krillpubd/krill.pid"

  # Note, there is no built in log rotation in Krill. You may
  # want to use the syslog facility instead. (see full defaults file below)
  log_file = "/path/to/your/krill.log"

  # We recommend that you let the Krill daemon listen on localhost
  # only, and use a proxy with proper HTTPS set up in front of it.
  # However, you should configure the 'service_uri' property in your
  # configuration file, so that your CAs will be able to connect to
  # your server to publish. You should provide the 'base' hostname
  # and optional port only. The actual URI that your CAs will connect
  # to is: $service_uri/rfc8181
  service_uri = "https://your.host/"

  # We also recommend that you archive old publication events and
  # use a process to either delete the archived data, or move it
  # to long term storage where space is not an issue.
  #
  # If you don't do this, then all updated from CAs will be preserved.
  # Typically this will mean that you get a new manifest and CRL file
  # every 16 hours, on top of any ROA changes. This can add up over
  # time.
  #
  # When archiving data will be moved to the following directory:
  # $data_dir/pubd/0/archived
  archive_threshold_days = 7

If you want to review all options, you can download the :download:`default config file<examples/krillpubd.conf>`.


Proxy for Remote Publishers
---------------------------

Krill runs the RFC8181 Publication Server. Remote publishers, CAs which use your
Publication Server, will need to connect to this under the `/rfc8181` path under
the `service_uri` that you specified in your server.

Make sure that you set up a proxy server such as NGINX, Apache, etc. which uses
a valid HTTPS certificate, and which proxies `/rfc8181` to Krill.

Note that you should not add any additional authentication mechanisms to this
location. RFC 8181 uses cryptographically signed messages sent over HTTP and is
secure. However, verifying messages and signing responses can be computationally
heavy, so if you know the source IP addresses of your publisher CAs, you may
wish to restrict access based on this.

Proxy for CLI and API
---------------------

If you are okay with only using the ``krillpubc`` CLI on the machine where you run
your Publication Server, then your safest option is to **not** proxy access to
the API.

However, if you need to use the CLI or API from other machines, then you should
proxy access to the path '/api' to Krill.

Configure the Repository
------------------------

.. Note:: We use the term **Publication Server** to describe the (Krill) server
          that CAs will connect to over the RFC 8181 protocol in order to publish
          their content. We use the term **Repository Server** to describe a server
          which makes this content available to RPKI Validators.



Synchronise Repository Data
"""""""""""""""""""""""""""

To actually serve the published content to Rsync and RRDP clients you will need
to run your own *repository* servers using tools such as Rsyncd and NGINX.

The Krill **Publication Server** will write the repository files under the data
directory specified in its configuration file:

.. code-block:: text

   $DATA_DIR/repo/rsync/current/    Contains the files for Rsync
   $DATA_DIR/repo/rrdp/             Contains the files for HTTPS (RRDP)

You can share the contents of these directories with your repository servers in
various ways.

**Shared Data**

One option is to use some kind of shared file system (NFS, clustered filesystem, network
storage) where the **Krill Publication Server** can write, and your **Repository Servers** can read.

If you go down this path, then make sure that the entire `$DATA_DIR/repo` is on a share.
In particular: don't use a mount point at `$DATA_DIR/repo/rsync/current` as this directory
is recreated by Krill whenever it publishes new data.

**Krill Sync**

Another approach is to synchronise the data written by the Publication Server to your
Repository Servers in a background process. A simple rsync command in crontab would
work most of the time, but unfortunately that approach will lead to regular issues where
inconsistent, or incomplete, data will be served to RPKI validators.

However, we have developed a separate tool `krill-sync <https://github.com/NLnetLabs/krill-sync>`_
which can be used for this purpose. Krill-sync essentially works by retrieving consistent
RRDP deltas from your back-end Publication Server to ensure that it can write consistent
sets of data to disk for use by your Repository Servers.

Rsync
"""""

The next step is to configure your rsync daemons to expose a 'module' for your
files. Make sure that the Rsync URI including the 'module' matches the
:file:`rsync_base` in your Krill configuration file. Basic configuration can
then be as simple as:

.. code-block:: bash

  $ cat /etc/rsyncd.conf
  uid = nobody
  gid = nogroup
  max connections = 50
  socket options = SO_KEEPALIVE

  [repo]
  path = /var/lib/krill/data/repo/rsync/current/
  comment = RPKI repository
  read only = yes

Note: we recommend that you use a limit for 'max connections'. Which value
works best for you depends on your local situation, so you may want to monitor
and tune this to your needs. Generally speaking though, it is better to limit
the number of connections because RPKI validators will simply try to reconnect,
rather then end up in a situation where your rsync server is unable to handle
requests.

RRDP
""""

For RRDP you will need to set up a web server of your choice and ensure that it
has a valid TLS certificate. Next, you can make the files found under, or copied
from :file:`$DATA_DIR/repo/rrdp` available here.

.. Note:: If desired, you can also use a **CDN** or your own caching infrastructure
          to reduce load. You could set it up to serve 'stale' content if your
          back-end system is unavailable to reduce the impact of short outages of
          your server. If you cache content make sure that you do not cache the
          main 'notification.xml' file (see more below) for longer than one minute
          (unless the back-end is unavailable). Other RRDP files will use unique
          names and can be cached for as long as you please.



Initialise Repository
"""""""""""""""""""""

You need to initialise your **Publication Server** using the base URIs as exposed
by your **Repository Servers**. Use the following command, well, make sure the
URIs reflect **your** setup of course:

.. code-block:: bash

  $ krillpubc server init --rrdp https://example.com/rrdp/ --rsync rsync://example.com/repo/

Provided that you also set up your Repository Servers, and that they are in sync,
you can now verify that the set up works. Try to get the 'notification.xml' file
under your base uri, e.g. https://example.com/rrdp/notification.xml. Verify that
access to your rsync server works by doing:

.. code-block:: bash

  $ rsync --list-only rsync://example.com/repo/

If you are satisfied that things work, you can proceed to add publishers for your
CAs. If not, then this is the moment to clear your Publication Server instance so
that it can be re-initialised:

.. code-block:: bash

  $ krillpubc server clear

Note that you can NOT clear a Publication Server instance if it has any active
publishers. Those CAs would not be aware that they would need to use new URIs
on their certificates.

If you should end up in this situation, then you could set up a new Publication
Server instead, and then migrate your existing CAs to that server, and then
remove your current server altogether. Alternatively, you can remove all
publishers from your server first, then clear and re-inialise it, and then
add your CAs again and migrate them to this newly initialised version.

In short: it is best to avoid this and ensure that your are happy with the
URIs used before adding publishers.



Manage Publishers
-----------------

As there is no UI support for this, you will need to use the command line
interface using the :ref:`krillc publisher<cmd_krillc_publishers>` subcommand
to manage publishers.

List Publishers
"""""""""""""""

You can list all current publishers using the following command:

.. code-block:: bash

  $ krillpubc list
  Publishers: ca


Add a Publisher
"""""""""""""""

In order to add a CA as a publisher you will need to get its RFC 8183 Publisher
Request XML. If you had no repository defined in your CA, you can get this XML
from the UI, as described :ref:`here<doc_krill_using_ui_repository_setup>`.

To add the Krill CA client XML to your server use the following:

.. code-block:: bash

  $ krillpubc add --request <path-to-xml> [--publisher <publisher-handle>]

If ``--publisher`` is not specified then the publisher identifier handle will be
taken from the XML. Handles need to be unique. So, you may want or need to
override this - especially if you provide your Publication Server as a service
to others. The publisher will learn the handle you chose in the response
that they will get, so it is perfectly safe and within the RFC protocol to
override it.

If successful this will show the response XML. But, you can also get this
response XML for a configured publisher using the following:

.. code-block:: bash

  $ krillpubc response --publisher <publisher-handle>

Migrate existing Krill CAs
--------------------------

The Krill CA server has support for migrating your CAs from one Publication
Server to another. A possible use case for this is that your RIR does not
provide an RPKI publication service today, but they may provide one in future.

The Krill UI does not support migrating the repository of your CA yet, as
this is a bit of a corner case. If there is operator demand for this we will
add support, but for now you can archive this trough the command line interface
connecting to your Krill instance that hosts your CA.

First you will need to get your CA's Publication Request XML using the
following:

.. code-block:: bash

  $ krillc repo request

You then need to give this XML to your Publication Server, be it provided by
a third party or managed by yourself as described above. After receiving the
Repository Response XML you can then update your CA's repository using:

.. code-block:: bash

  $ krillc repo update -reponse <path-to-xml>

Krill will then make sure that objects are moved properly, and that a new
certificate is requested from your parent(s) to match the new location.

When this is done your CA can be safely removed from the old Publication Server
altogether. Remove the publisher on the old Publication Server if you were self-hosting:

.. code-block:: bash

  $ krillpubc remove --publisher <publisher-handle>
