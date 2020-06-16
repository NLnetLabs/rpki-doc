.. _doc_krill_publication_server:

Running a Publication Server
============================

.. Important:: It is highly recommended to use an RPKI publication server
               provided by your parent CA, if available. This relieves you of
               the responsibility to keep a public rsync and web server
               available at all times.


If you need to run your own Publication Server using Krill, then we recommend
that you use a separate Krill instance acting as a repository only. This setup
allows for much easier reconfiguration (more on this below), and it allows that
other CAs - for example a delegated CA for one of your business units also
publish at this same Publication Server.

.. figure:: img/parent-child-repo.*
    :align: center
    :width: 100%
    :alt: Running your own publication server

    Running a publication server for yourself and your children


Configuring a Krill Repository
------------------------------

.. Note:: The Krill UI is not currently aimed at using Krill as a repository
          server. For example when visiting the UI of a Krill instance intended
          for use only as a repository and not as a CA, it will still prompt you
          on first use to create a CA. There is also no support via the UI for
          managing the repository, for example it is not possible via the UI to
          complete a child request to register with the repository.


Krill can be set up to run as a Publication Server through its configuration
file. If enabled, the Publication Server is created on start-up. After this
any updates to the configuration will *NOT* be reflected in the Publication
Server.

For this reason you should double check the values used for the public URIs to
your repository server carefully before the set-up. Using a dedicated Krill
instance for the Publication Server will allow you to simply destroy and replace
the instance should it have been misconfigured.

The easiest way to make a configuration file is by using
:ref:`krillc config<cmd_krillc_config>`  to generate the required configuration
for you. For example:

.. parsed-literal::

  :ref:`krillc config repo<cmd_krillc_config_repo>` \\
     --server "https://rfc8183.example.net/" \\
     --token correct-horse-battery-staple \\
     --data ~/data/ \\
     --rrdp "https://rpki.example.net/rrdp/" \\
     --rsync "rsync://rpki.example.net/repo/" > krill.conf

Make sure that the ``--server`` option reflects a base URI that your Krill CA
publication clients can reach, and make sure that this URI is exposed using
a proxy server that has a proper HTTPS certificate configured.

Make sure that the ``--rrdp`` and ``--rsync`` options match the configuration of your
"Repository Servers" which make your repository available over HTTPS and rsync
to Relying Parties.

.. Note:: It would have been better to make the Publication Server configuration
          something that should be done run-time, as this would match more
          intuitively with the fact that the `server`, `rrdp` and `rsync` URIs
          cannot be changed through the configuration file.

          In a future release of Krill we may do exactly that. But, even if we
          do it would be ill advised to allow changing these URIs at run time,
          as there would be no way for the Krill Publication Server to inform
          its publishers about any change.

          So, in short, this needs to be set up correctly once. If it turns out
          to be wrong, then a new Publication Server should be set up and any
          existing publishers should be migrated as described below.

Configuring Repository Servers
------------------------------

Krill runs the RFC8181 Publication Server. To actually serve the published
content to Rsync and RRDP clients you will need to run your own *repository*
servers using tools such as Rsyncd and NGINX.

Krill will write the repository files under the data directory specified in its
configuration file:

.. code-block:: text

   $DATA_DIR/repo/rsync/current/    Contains the files for Rsync
   $DATA_DIR/repo/rrdp/             Contains the files for HTTPS (RRDP)

You can share the contents of these directories with your repository servers in
various ways. It is possible to have a redundant shared file system where the
Krill Publication Server can write, and your repository servers can read.
Alternatively, you can synchronise the contents of these directories in another
way, such as rsyncing them over every couple of minutes.

If you are using a shared file system, please note that the rsync
:file:`/current` directory cannot be the mount point. Krill tries to write the
entire repository to a new folder under :file:`$DATA_DIR/repo/rsync` and then
renames it. This is done to minimise issues with files being updated while
relying party software is fetching data.

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

RRDP
""""

For RRDP you will need to set up a web server of your choice and ensure that it
has a valid TLS certificate. Next, you can make the files found under, or copied
from :file:`$DATA_DIR/repo/rrdp` available here. Make sure that the public URI
to the RRDP base directory matches the value of ``rrdp_service_uri`` in your
:file:`krill.conf` file, or the ``--rrdp`` option if you generated the
configuration.

If desired, you can also use a CDN in front of this server to further reduce
your load and uptime requirements. If you do, make sure that the public URI
matches the directive in :file:`krill.conf`, because this will be used in your
RPKI certificate.

RFC 8181 (publication protocol)
"""""""""""""""""""""""""""""""

Make sure that your Krill Publication Server can be reached by your Krill CA
clients. The best way to do this, is by setting up a web server, similar to the
RRDP set up above, which proxies access to URIs starting with ``/rfc8181`` under
the hostname you specified with the ``--server`` option through to your Krill
Publication Server.


Publishing in the Repository
----------------------------

As there is no UI support for this, you will need to use the command line
interface using the :ref:`krillc publisher<cmd_krillc_publishers>` subcommand
to manage publishers.

This subcommand will allow you to add your Krill CA client's RFC8181 Publisher
Request XML, and obtain a Repository Response XML for it. From the client CA's
perspective this part of the process is exactly as described :ref:`here<doc_krill_using_ui_repository_setup>`.

To add the Krill CA client XML to your server use the following:

.. code-block:: bash

  $ krillc publishers add --request <path-to-xml> [--publisher publisher]

If ``--publisher`` is not specified then the publisher identifier handle will be
taken from the XML. Handles need to be unique. So, you may want or need to
override this - especially if you provide your Publication Server as a service
to others.

If successful this will show the response XML. But, you can also get this
response XML for a configured publisher using the following:

.. code-block:: bash

  $ krillc publishers response --publisher publisher



Migrating the Repository
------------------------

If you find that there is an issue with your repository or, for example, you
want to change its domain name, you can set up a new Krill instance with an
embedded repository. When you are satisfied that the new one is correct, you
can migrate your CA to it by adding them as a publisher under the new
repository server, and then updating your CA to use the new repository.

Updating the repository of your Krill CAs is currently not possible using the
UI, but you can archive this trough the command line interface connecting to
your Krill instance that hosts your CA.

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
certificate is requested from your parent(s) to match the new location. This
scenario would also apply when your RIR starts providing a repository service.
You can update your CA to start publishing there instead.
