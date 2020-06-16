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

Assuming you have a newly installed Debian or Ubuntu machine, you will need to
install the C toolchain, OpenSSL, curl and Rust. You can then install Krill
using Cargo.

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
   cargo install -f krill

.. Note:: Using a fully qualified domain name, configuring a real TLS
          certificate such as Let's Encrypt, running on a different port and
          exposing Krill securely to other machines is all possible, but goes
          beyond the scope of this Quick Start.

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
Krill relies on a relatively new version of Rust, currently 1.40 or
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
""""""""

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
TLS certificate issued by a CA works, but has not been tested extensively.

For a robust solution, we recommend that you use a proxy server such as NGINX or
Apache if you intend to make Krill available to the Internet. Also, setting up a
widely accepted TLS certificate is well documented for these servers.

.. Warning:: We recommend that you do **not** make Krill available publicly.
             You can use the default where Krill will expose its CLI, API and
             UI on ``https://localhost:3000/`` only. You do not need to have
             Krill available externally, unless you intend to provide
             certificates or a publication server to third parties.

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
embedded publication server.
