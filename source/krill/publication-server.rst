.. _doc_krill_publication_server:

Running a Publication Server
============================

.. Important:: It is highly recommended to use an RPKI publication server
               provided by your parent CA, if available. This relieves you of
               the responsibility to keep a public rsync and web server
               available at all times.

It could also be considered good for the RPKI ecosystem as a whole to have at
least some centralisation of publication. If Relying Party software around the
world has to periodically fetch data from possibly hundreds of RPKI repositories
with various degrees of availability and latency, this would not result in
speedy updates.

Please keep in mind that it is crucial to ensure that your certificate and ROAs
remain available at all times. When the Krill CA is unavailable you will not be
able to make updates to your ROAs, and Krill will not (re-)publish objects at a
repository. This does not need to affect relying parties as long as the
repository remains available, and Krill comes back before objects start to
become stale. You have 8 hours with the current built-in defaults.

That being said, only Brazilian NIC.br members can configure a hosted
publication server without manual intervention at this time. APNIC members can
get access to their publication server upon request. Everyone else
will need to host their own repository for now.

When running your own repository, it is advisable to use multiple rsync servers
and multiple web servers. You can also use a Content Delivery Network (CDN) in
front of your web servers. Please note that the official name for the HTTPS
based transport is the RPKI Repository Delta Protocol (RRDP), so you will see
this abbreviation used throughout the documentation.

Creating the Embedded Repository
--------------------------------

.. Warning:: Please note it is practically impossible to change the
             configuration of Krill's embedded repository after it has been
             initialised. For production environments where you may want to
             change strategies over time we recommend running a separate Krill
             instance running as a repository only, as described in
             :ref:`doc_krill_remote_publishing`.

.. Note:: The Krill UI is not currently aimed at using Krill as a repository
          server. For example when visiting the UI of a Krill instance intended
          for use only as a repository and not as a CA, it will still prompt you
          on first use to create a CA. There is also no support via the UI for
          managing the repository, for example it is not possible via the UI to
          complete and child request to register with the repository.

Krill can use an embedded repository to publish RPKI objects. It can generate
the required configuration for you using the :ref:`krillc config<cmd_krillc_config>`
subcommand. This ensures the syntax is correct, as for example trailing slashes
are required. Use this command with your own values, using domain names pointing
to servers that are publicly reachable.

.. parsed-literal::

  :ref:`krillc config repo<cmd_krillc_config_repo>` \\
     --token correct-horse-battery-staple \\
     --data ~/data/ \\
     --rrdp "https://rpki.example.net/rrdp/" \\
     --rsync "rsync://rpki.example.net/repo/" > krill.conf

Configuring Repository Servers
------------------------------

Krill runs the publication server. To actually serve the published content to
Rsync and RRDP clients you will need to run your own *repository* servers using
tools such as Rsyncd and NGINX.

Krill will write the repository files under its data directory:

.. code-block:: text

   $DATA_DIR/repo/rsync/current/    Contains the files for Rsync
   $DATA_DIR/repo/rrdp/             Contains the files for HTTPS (RRDP)

You can share the contents of these directories with your repository servers in
various ways. It is possible to have a redundant shared file system where the
Krill CA can write, and your repository servers can read. Alternatively, you can
synchronise the contents of these directories in another way, such as
Rsyncing them over every couple of minutes.

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
:file:`krill.conf` file.

If desired, you can also use a CDN in front of this server to further reduce
your load and uptime requirements. If you do, make sure that the public URI
matches the directive in :file:`krill.conf`, because this will be used in your
RPKI certificate.

Publishing in the Repository
----------------------------

See :ref:`remote_publishing_to_krill_repo` in :ref:`doc_krill_remote_publishing`.

Migrating the Repository
------------------------

If you find that there is an issue with your repository or, for example, you
want to change its domain name, you can set up a new Krill instance with an
embedded repository. When you are satisfied that the new one is correct, you
can migrate your CA to it by adding them as a publisher under the new
repository server, and then updating your CA to use the new repository.

Krill will then make sure that objects are moved properly, and that a new
certificate is requested from your parent(s) to match the new location. This
scenario would also apply when your RIR starts providing a repository service.
You can update your CA to start publishing there instead.

