.. _doc_krill_instal_and_run:

Install and Run
===============

Before you can start to use Krill you will need to install, configure and run
the Krill application somewhere. Please follow the steps below and you will be
ready to :ref:`doc_krill_get_started` or start :ref:`doc_krill_testing`.

Installation
------------

Getting started with Krill is quite easy either building from Cargo or running
with Docker. In case you intend to serve your RPKI certificate and ROAs to the
world yourself or you want to offer this as a service to others, you will also
need to have a public Rsyncd and HTTPS web server available.

Krill can also be se up as a highly available, scalable service using
:ref:`doc_krill_manager`.  A 1-Click App on the DigitalOcean Marketplace can set
up Krill with all required components, along with integration points for
monitoring and log analysis.

Quick Start
"""""""""""

For recent Debian and Ubuntu releases you can download, install and run a ``.deb``
package from the NLnet Labs package repository.

.. Note:: If you had previously installed Krill using ``cargo install krill`` you should
          first use ``cargo uninstall krill`` before installing a ``.deb`` package Otherwise
          the cargo installed binaries for ``krill`` and ``krillc`` may take precednce in
          your shell ``$PATH`` which could be confusing.

1. Add the line below that corresponds to your operating system to ``/etc/apt/sources.list`` or ``/etc/apt/sources.list.d/``:

.. code-block:: bash

  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/debian/ stretch main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/debian/ buster main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ xenial main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ bionic main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ focal main

2. Add the repository signing key to the listed of trusted keys:

.. code-block:: bash

  wget -qO- https://packages.nlnetlabs.nl/aptkey.asc | sudo apt-key add -

3. Install and start Krill:

.. code-block:: bash

  sudo apt update
  sudo apt-get install krill
  # review / edit /etc/krill.conf
  sudo systemctl enable --now krill

Alternatively, you can build from sources. Assuming you have a newly installed Debian or
Ubuntu machine, you will need to install the C toolchain, OpenSSL, curl and
Rust. You can then install Krill using Cargo.

After the installation has completed, first create a data directory in a
location of your choice. Next, generate a basic configuration file specifying a
`secret token <https://xkcd.com/936/>`_ and make sure to refer to the data
directory you just created. Finally, start Krill pointing to your configuration
file.

.. code-block:: bash

  apt install build-essential libssl-dev openssl pkg-config curl
  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
  source ~/.cargo/env
  cargo install krill
  mkdir ~/data
  krillc config simple --token correct-horse-battery-staple --data ~/data/ > ~/data/krill.conf
  krill --config ~/data/krill.conf

Krill now exposes its user interface and API on ``https://localhost:3000``
using a self-signed TLS certificate. You can go to this address in a web
browser, accept the certificate warning and start configuring your RPKI
Certificate Authority. A Prometheus endpoint is available at ``/metrics``.

If you have an older version of Rust and Krill, you can update via:

.. code-block:: bash

   rustup update
   cargo install --force krill

.. Note:: Using a fully qualified domain name, configuring a real TLS
          certificate such as Let's Encrypt, running on a different port and
          exposing Krill securely to other machines is all possible, but goes
          beyond the scope of this Quick Start.

Installing with APT/dpkg
""""""""""""""""""""""""

Pre-built Debian/Ubuntu packages are available for recent operating system
versions on x86_64 platforms. These can be installed using the standard ``apt``,
``apt-get`` and ``dpkg`` commands as usual.

Unlike with installing with Cargo there is no need to have Rust or a C toolchain
installed. Additionally the packages come with systemd service files for easy
start/stop of the Krill daemon and with short Linux man pages.

.. Note:: For the oldest platforms, Ubuntu 16.04 LTS and Debian 9, the packaged
          krill binary is statically linked with OpenSSL 1.1.0 as this is the
          minimum version required by Krill and is higher than available in the
          official package repositories for those platforms.

To install Krill from the NLnet Labs package repository:

1. Run ``cargo uninstall krill`` if you previously installed Krill with Cargo.
2. Add the line below that corresponds to your operating system to ``/etc/apt/sources.list`` or ``/etc/apt/sources.list.d/``:

.. code-block:: bash

  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/debian/ stretch main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/debian/ buster main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ xenial main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ bionic main
  deb [arch=amd64] https://packages.nlnetlabs.nl/linux/ubuntu/ focal main

2. Add the repository signing key to the listed of trusted keys:

.. code-block:: bash

  wget -qO- https://packages.nlnetlabs.nl/aptkey.asc | sudo apt-key add -

3. Install Krill using ``sudo apt-get update`` and ``sudo apt-get install krill``.
4. Review the generated configuration file at ``/etc/krill.conf``.
   **Pay particular attention** to the ``service_uri`` and ``auth_token``
   settings. Tip: The configuration file was generated for you using the
   ``krillc config simple`` command.
5. Once happy with the settings use ``sudo systemctl enable --now krill`` to instruct
   systemd to enable the Krill service at boot and to start it immediately.

The krill daemon runs as user ``krill`` and stores its data in ``/var/lib/krill``.
You can manage the Krill daemon using the following commands:

- Review the Krill logs with ``journalctl -u krill``, or view just the most recent entries with ``sytemctl status krill``.

- Stop Krill with ``sudo systemctl stop krill``.

- Learn more about Krill using ``man krill`` and ``man krillc``.

- Upgrade Krill by running ``apt-get update`` and ``apt-get install krill``.

.. Note:: Due to `issue #280 <https://github.com/NLnetLabs/krill/issues/280>`_,
          when upgrading with ``apt-get`` it is currently necessary to restart
          Krill manually after upgrade with ``sudo systemctl restart krill``.
          This issue will be resolved in the next major release.

Installing with Cargo
"""""""""""""""""""""

There are three things you need for Krill: Rust, a C toolchain and OpenSSL.
You can install Krill on any Operating System where you can fulfil these
requirements, but we will assume that you will run this on a UNIX-like OS.

Rust
~~~~

The Rust compiler runs on, and compiles to, a great number of platforms,
though not all of them are equally supported. The official `Rust
Platform Support <https://forge.rust-lang.org/platform-support.html>`_
page provides an overview of the various support levels.

While some system distributions include Rust as system packages,
Krill relies on a relatively new version of Rust, currently 1.42 or
newer. We therefore suggest to use the canonical Rust installation via a
tool called :command:`rustup`.

To install :command:`rustup` and Rust, simply do:

.. code-block:: bash

   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Alternatively, visit the `official Rust website
<https://www.rust-lang.org/tools/install>`_ for other installation methods.

You can update your Rust installation later by running:

.. code-block:: bash

   rustup update

For some platforms, :command:`rustup` cannot provide binary releases to install
directly. The `Rust Platform Support
<https://forge.rust-lang.org/platform-support.html>`_ page lists
several platforms where official binary releases are not available,
but Rust is still guaranteed to build. For these platforms, automated
tests are not run so it’s not guaranteed to produce a working build, but
they often work to quite a good degree.

One such example that is especially relevant for the routing community
is OpenBSD. On this platform, `patches
<https://github.com/openbsd/ports/tree/master/lang/rust/patches>`_ are
required to get Rust running correctly, but these are well maintained
and offer the latest version of Rust quite quickly.

Rust can be installed on OpenBSD by running:

.. code-block:: bash

   pkg_add rust

Another example where the standard installation method does not work is
CentOS 6, where you will end up with a long list of error messages about
missing assembler instructions. This is because the assembler shipped with
CentOS 6 is too old.

You can get the necessary version by installing the `Developer Toolset 6
<https://www.softwarecollections.org/en/scls/rhscl/devtoolset-6/>`_ from the
`Software Collections
<https://wiki.centos.org/AdditionalResources/Repositories/SCL>`_ repository. On
a virgin system, you can install Rust using these steps:

.. code-block:: bash

   sudo yum install centos-release-scl
   sudo yum install devtoolset-6
   scl enable devtoolset-6 bash
   curl https://sh.rustup.rs -sSf | sh
   source $HOME/.cargo/env

C Toolchain
~~~~~~~~~~~

Some of the libraries Krill depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. For example,
:command:`apt install build-essential` will install everything you need on
Debian/Ubuntu.

If you are unsure, try to run :command:`cc` on a command line and if there’s a
complaint about missing input files, you are probably good to go.

OpenSSL
~~~~~~~

Your system will likely have a package manager that will allow you to install
OpenSSL in a few easy steps. For Krill, you will need :command:`libssl-dev`,
sometimes called :command:`openssl-dev`. On Debian-like Linux distributions,
this should be as simple as running:

.. code-block:: bash

    apt install libssl-dev openssl pkg-config


Building
~~~~~~~~

The easiest way to get Krill is to leave it to cargo by saying:

.. code-block:: bash

   cargo install krill

If you want to update an installed version, you run the same command but
add the ``-f`` flag, a.k.a. force, to approve overwriting the installed
version.

The command will build Krill and install it in the same directory
that cargo itself lives in, likely :file:`$HOME/.cargo/bin`. This means
Krill will be in your path, too.


Generate Configuration File
---------------------------

After the installation has completed, there are just two things you need to
configure before you can start using Krill. First, you will need a data
directory, which will store everything Krill needs to run. Secondly, you will
need to create a basic configuration file, specifying a secret token and the
location of your data directory.

The first step is to choose where your data directory is going to live and to
create it. In this example we are simply creating it in our home directory.

.. code-block:: bash

  mkdir ~/data

Krill can generate a basic configuration file for you. We are going to specify
the two required directives, a secret token and the path to the data directory,
and then store it in this directory.

.. parsed-literal::

  :ref:`krillc config simple<cmd_krillc_config_simple>` --token correct-horse-battery-staple --data ~/data/ > ~/data/krill.conf

.. Note:: If you wish to run a self-hosted RPKI repository with Krill you will
          need to use a different ``krillc config`` command. See :ref:`doc_krill_publication_server`
          for more details.

You can find a full example configuration file with defaults in `the
GitHub repository
<https://github.com/NLnetLabs/krill/blob/master/defaults/krill.conf>`_.


Start and Stop the Daemon
-------------------------

There is currently no standard script to start and stop Krill. You could use the
following example script to start Krill. Make sure to update the
``DATA_DIR`` variable to your real data directory, and make sure you saved
your :file:`krill.conf` file there.

.. code-block:: bash

  #!/bin/bash
  KRILL="krill"
  DATA_DIR="/path/to/data"
  KRILL_PID="$DATA_DIR/krill.pid"
  CONF="$DATA_DIR/krill.conf"
  SCRIPT_OUT="$DATA_DIR/krill.log"

  nohup $KRILL -c $CONF >$SCRIPT_OUT 2>&1 &
  echo $! > $KRILL_PID

You can use the following sample script to stop Krill:

.. code-block:: bash

  #!/bin/bash
  DATA_DIR="/path/to/data"
  KRILL_PID="$DATA_DIR/krill.pid"

  kill `cat $KRILL_PID`

.. _proxy_and_https:

Proxy and HTTPS
---------------

Krill uses HTTPS and refuses to do plain HTTP. By default Krill will generate a
2048 bit RSA key and self-signed certificate in :file:`/ssl` in the data
directory when it is first started. Replacing the self-signed certificate with a
TLS certificate issued by a CA works, but has not been tested extensively. By
default Krill will only be available under ``https://localhost:3000``.

If you need to access the Krill UI or API (also used by the CLI) from another
machine you can use use a proxy server such as NGINX or Apache to proxy requests
to Krill. This proxy can then also use a proper HTTPS certificate and production
grade TLS support.


Proxy Krill UI
""""""""""""""

The Krill UI and assets are hosted directly under the base path `/`. So, in
order to proxy to the Krill UI you should proxy ALL requests under `/` to the
Krill back-end.

Note that although the UI and API are protected by a token, you should consider
further restrictions in your proxy setup - like restrictions on source IP, or
you may want to have your own authentication added.


Proxy Krill as Parent
"""""""""""""""""""""

If you delegated resources to child CAs then you will need to ensure that these
children can reach your Krill. Child requests for resource certificates are
directed to the `/rfc6492` under the `service_uri` that you defined in your
configuration file.

Note that contrary to the UI you should not add any additional authentication
mechanisms to this location. RFC 6492 uses cryptographically signed messages
sent over HTTP and is secure. However, verifying messages and signing responses
can be computationally heavy, so if you know the source IP addresses of your
child CAs, you may wish to restrict access based on this.

Proxy Krill as Publication Server
"""""""""""""""""""""""""""""""""

If you are running Krill as a Publication Server, then you should read
:ref:`here<doc_krill_publication_server>` how to do the Publication Server
specific set up.

.. Warning:: We recommend that you do **not** make Krill available to the public
             internet unless you really need remote access to the UI or API, or
             you are serving as parent CA or Publication Server for other CAs.


Backup and Restore
------------------

To back-up Krill:

- Stop Krill
- Backup your data directory
- Start Krill

We recommend that you stop Krill because there can be a race condition where
Krill was just in the middle of saving its state after performing a background
operation. We will most likely add a process in future that will allow you to
back up Krill in a consistent state while it is running.

To restore Krill just put back your data directory and make sure that you refer
to it in the configuration file that you use for your Krill instance.

Used Disk Space
---------------

Krill stores all of its data under the ``DATA_DIR``. For users who will operate
a CA under an RIR / NIR parent the following sub-directories are relevant:

+---------+------------------------------------------------------+
| Dir     | Purpose                                              |
+=========+======================================================+
| ssl     | Contains the HTTPS key and cert used by Krill        |
+---------+------------------------------------------------------+
| cas     | Contains the history of your CA in raw JSON format   |
+---------+------------------------------------------------------+
| rfc6492 | Contains all messages exchanged with your parent     |
+---------+------------------------------------------------------+
| rfc8181 | Contains all messages exchanged with your repository |
+---------+------------------------------------------------------+

The space used by the latter two directories can grow significantly over time.
We think it may be a good idea to have an audit trail of all these exchanges.
However, if space is a concern you can safely archive or delete the contents of
these two directories.

In a future version of Krill we will most likely only store the exchanges where
either an error was returned, or your Krill instance asked for a change to be
made at the parent side: like requesting a new certificate, or publishing an
object. The periodic exchanges where your CA asks the parent for its
entitlements will then no longer be logged.

Krill Upgrades
--------------

It is our goal that future versions of Krill will continue to work with the
configuration files and saved data from version 0.4.1 and above. However, please
read the changelog to be sure.

The normal process would be to:

- Install the new version of Krill
- Stop the running Krill instance
- Start Krill again, using the new binary, and the same configuration

Note that after a restart you may see a message like this in your log file:

.. code-block:: text

  2020-01-28 13:41:03 [WARN] [krill::commons::eventsourcing::store] Could not
  deserialize snapshot json '/root/krill/data/pubd/0/snapshot.json', got error:
  'missing field `stats` at line 296 column 1'. Will fall back to events.

You can safely ignore this message. Krill is telling you that the definition of
a struct has changed and therefore it cannot use the :file:`snapshot.json` file
that it normally uses for efficiency. Instead, it needs to build up the current
state by explicitly re-applying all the events that happened to your CA and/or
publication server.
