.. _doc_krill_instal_and_run:

Install and Run
===============

Before you can start to use Krill you will need to install, configure and run
the Krill application somewhere. Please follow the steps below and you will be
ready to :ref:`doc_krill_get_started`.

Installation
------------

Getting started with Krill is quite easy either building from Cargo or running
with Docker. In case you intend to serve your RPKI certificate and ROAs to the
world yourself or you want to offer this as a service to others, you will also
need to have a public Rsyncd and HTTPS web server available.

.. Warning:: Krill does NOT support clustering at this time. You can achieve
             high availability by doing a fail-over to a standby *inactive*
             installation using the same data and configuration. However, you
             cannot have multiple active instances. This
             `feature <https://github.com/NLnetLabs/krill/issues/20>`_ is on our
             long term roadmap.

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

The krill daemon runs as user ``krill`` and stores its data in
``/var/lib/krill``. You can manage the Krill daemon using the following
commands:

- Review the Krill logs with ``journalctl -u krill``, or view just the most recent entries with ``sytemctl status krill``.

- Stop Krill with ``sudo systemctl stop krill``.

- Learn more about Krill using ``man krill`` and ``man krillc``.

- Upgrade Krill by running ``apt-get update`` and ``apt-get install krill``.

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

Used Disk Space
---------------

Krill stores all of its data under the ``DATA_DIR``. For users who will operate
a CA under an RIR / NIR parent the following sub-directories are relevant:

+-----------------+------------------------------------------------------------+
| Dir             | Purpose                                                    |
+=================+============================================================+
| data_dir/ssl    | Contains the HTTPS key and cert used by Krill              |
+-----------------+------------------------------------------------------------+
| data_dir/cas    | Contains the history of your CA(s) in raw JSON format      |
+-----------------+------------------------------------------------------------+
| data_dir/pubd   | Contains the history of your Publication Server if enabled |
+-----------------+------------------------------------------------------------+

.. Warning::  Note that old versions of Krill also used the directories
              `data_dir/rfc8181` and `data_dir/rfc6492` for storing all
              protocol messages exchanged between your CAs and their parent
              and repository. If they are still present on your system, you
              can safely remove them and save space - potentially quite a bit
              of space.

Archiving
"""""""""

Krill offers the option to archive old, less relevant, historical information
related to publication. You can enable this by setting the option ``archive_threshold_days``
in your configuration file. If set Krill will move all publication events older
than the specified number of days to a subdirectory called `archived` under the
relevant data directory: ``data_dir/pubd/0/archived`` if you are using Krill as a
Publication Server and ``data_dir/cas/<your-ca-name>/archived`` for each of your
CAs.

You can set up a cronjob to delete these events once and for all, but we
recommend that you save them in long term storage if you can. The reason is that
if (and only if) you have this data, you will be able to rebuild the complete
Krill state based on its *audit* log of events, and irrevocably prove that no
changes were made to Krill other than the changes recorded in the audit trail.
We have no tooling for this yet, but we have an `issue <https://github.com/NLnetLabs/krill/issues/331>`_
on our backlog.

Saving State Changes
--------------------

You can skip this section if you're not interested in the gory details. However,
understanding this section will help to explain how backup and restore works in
Krill, and why a standby fail-over node can be used, but Krill's locking and
storage mechanism needs to be changed in order to make
`multiple active nodes <https://github.com/NLnetLabs/krill/issues/20>`_
work.

State changes in Krill are tracked using *events*. Krill CA(s) and Publication
Servers are versioned. They can only be changed by applying an *event* for a
specific version. An *event* just contains the data that needs to be changed.
Crucially, they cannot cause any side effects. As such the overall state can
always be reconstituted by applying all past events. This concept is called
*event-sourcing*, and in this context the CAs and Publication Servers are
so-called "Aggregates".

Events are not applied directly. Rather, users of Krill and background jobs will
send their intention to make a change through the API, which then translates
this into a so-called *command*. Krill will then *lock* the target aggregate
and send the command to it. This locking mechanism is not aware of any
clustering, and it's a primary reason why Krill cannot run as an active-active
cluster just yet.

Upon receiving a command the aggregate (your CA etc.) will do some work. In some
cases a command can have a side-effect. For example it may instruct your CA to
create a new key pair, after receiving entitlements from its parent. The key pair
is random — applying a command again would result in a new random key pair.
Remember that commands are not re-applied to aggregates, only their resulting
events are. Thus in this example there would be an event caused that contains
the resulting key pair.

After receiving the command, the aggregate will return one of the following:

1. An error

Usually this means that the command is not applicable to the aggregate state.
For example, you may have tried to remove a ROA which does not exist.

When Krill encounters such an error, it will store the command with some
meta-information like the time the command was issued, and a summary of the
error, so that it can be seen in the history. It will then unlock the aggregate,
so that the next command can be sent to it.

2. No error, zero events

In this case the command turned out to be a *no-op*, and Krill just unlocks the
aggregate. The command sequence counter is not updated, and the command is not
saved. This is used as a feature whenever the 'republish' background job kicks
in. A 'republish' command is sent, but it will only have an actual effect if
there was a need to republish — e.g. a manifest would need to be re-issued
before it would expire.

3. One or more events

In this case there *is* a desired state change in a Krill aggregate.

Krill will now apply and persist the changes in the following order:

* Each event is stored. If an event already exists for a version, then then the
  update is aborted. Because Krill cannot run as a cluster, and it uses locking
  to ensure that updates are done in sequence, this will only fail on the first
  event if a user tried to issue concurrent updates to the same CA
* On every fifth event a snapshot of the state is saved to a new file. If this is
  successful then the old snapshot (if there is one) is renamed and kept as a
  backup snapshot. The new snapshot is then renamed to the 'current' snapshot.
* When all events are saved, the command is saved enumerating all resulting
  events, and including meta-information such as the time that the time that the
  command was executed. And when `multiple users <https://github.com/NLnetLabs/krill/issues/294>`_
  will be supported, this will also include *who* made a change.
* Finally the version information file for the aggregate is updated to indicate
  its current version, and command sequence counter.

.. Warning:: Krill will crash, **by design**, if there is any failure in saving
             any of the above files to disk. If Krill cannot persist its state
             it should not try to carry on. It could lead to disjoints between
             in-memory and on-disk state that are impossible to fix. Therefore,
             crashing and forcing an operator to look at the system is the only
             sensible thing Krill can now do. Fortunately, this should not
             happen unless there is a serious system failure.

Loading State at Startup
------------------------

Krill will rebuild its internal state whenever it starts. If it finds that there
are surplus events or commands compared to the latest information state for any
of the aggregates, then it will assume that they are present because, either
Krill stopped in the middle of writing a transaction of changes to disk, or your
backup was taken in the middle of a transaction. Such surplus files are backed
up to a subdirectory called ``surplus`` under the relevant data directory:
``data_dir/pubd/0/surplus`` if you are using Krill as a Publication Server and
``data_dir/cas/<your-ca-name>/surplus`` for each of your CAs.


Recover State at Startup
------------------------

When Krill starts, it will try to go back to the last possible **recoverable**
state if:

* it cannot rebuild its state at startup due to data corruption
* the environment variable: ``KRILL_FORCE_RECOVER`` is set
* the configuration file contains ``always_recover_data = true``

Under normal circumstances, i.e. where there is no data corruption, performing
this recovery will not be necessary. It can also take significant time due to
all the checks performed. So, we do **not recommend** forcing this.

Krill will try the following checks and recovery attempts:

* Verify each recorded command and its effects (events) in their historical order.
* If any command or event file is corrupt it will be moved to a subdirectory
  called ``corrupt`` under the relevant data directory, and all subsequent
  commands and events will be moved to a subdirectory called ``surplus`` under
  the relevant data directory.
* Verify that each snapshot file can be parsed, if it can't then this file is
  moved to relevant the ``corrupt`` sub-directory.
* If a snapshot file could not be parsed, try to parse the backup snapshot. If
  this file can't be parsed, move it to the relevant ``corrupt`` sub-directory.
* Try to rebuild the state to the last recoverable state, i.e. the last known
  good event. Note that if this pre-dates the available snapshots, or, if no
  snapshots are available this means that Krill will try to rebuild state by
  replaying all events. If you had enabled archiving of events, it will not be
  able rebuild state.
* If rebuilding state failed, Krill will now exit with an error.

Note that in case of data corruption Krill may be able to fall back to an
earlier recoverable state, but this state may be far in the past. You should
always verify your ROAs and/or delegations to child CAs in such cases.

Of course, it's best to avoid data corruption in the first place. Please monitor
available disk space, and make regular backups.

Backup / Restore
----------------

Backing up Krill is as simple as backing up its data directory. There is no need
to stop Krill during the backup. To restore put back your data directory and
make sure that you refer to it in the configuration file that you use for your
Krill instance. As described above, if Krill finds that the backup contain an
incomplete transaction, it will just fall back to the state prior to it.

.. Warning:: You may want to **encrypt** your backup, because the ``data_dir/ssl``
             directory contains your private keys in clear text. Encrypting
             your backup will help protect these, but of course also implies
             that you can only restore if you have the ability to decrypt.

Krill Upgrades
--------------

All Krill versions 0.4.1 and upwards can be automatically upgraded to the
current version. To do so we recommend that you:

* backup your krill data directories
* install the new version of Krill
* stop the running Krill instance
* start Krill again, using the new binary, and the same configuration

If Krill needs to do any data migrations it will do so automatically.

If you just want to test that these data migrations will work for your data,
you can do the following:

* copy your data directory to another system
* set the env variable ``KRILL_UPGRADE_ONLY=1``
* create a configuration file, and set ``data_dir=/path/to/your/copy``
* start up krill

Krill will then perform the data migrations, rebuild its state, and then exit
before doing anything else.

Krill Downgrades
----------------

Downgrading Krill data is not supported. So, downgrading can only be achieved
by installing a previous version of Krill and restoring a backup from before
your upgrade.

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

The Krill UI and assets are hosted directly under the base path ``/``. So, in
order to proxy to the Krill UI you should proxy ALL requests under ``/`` to the
Krill back-end.

Note that although the UI and API are protected by a token, you should consider
further restrictions in your proxy setup, such as restrictions on source IP or 
adding your own authentication.

Proxy Krill as Parent
"""""""""""""""""""""

If you delegated resources to child CAs then you will need to ensure that these
children can reach your Krill. Child requests for resource certificates are
directed to the ``/rfc6492`` directory under the ``service_uri`` that you
defined in your configuration file.

Note that contrary to the UI you should not add any additional authentication
mechanisms to this location. :RFC:`6492` uses cryptographically signed messages
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
