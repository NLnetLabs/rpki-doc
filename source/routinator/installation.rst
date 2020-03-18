.. _doc_routinator_installation:

Installation
============

Getting started with Routinator is really easy either building from `Cargo
<https://crates.io/crates/routinator>`_ or running with `Docker
<https://hub.docker.com/r/nlnetlabs/routinator/>`_. Alternatively, GÉANT has
created an `Ansible playbook <https://github.com/GEANT/rpki-validation-tools>`_
defining a role to deploy Routinator on Ubuntu.

Quick Start
-----------

Assuming you have a newly installed Debian or Ubuntu machine, you will
need to install rsync, the C toolchain and Rust. You can then install
Routinator and start it up as an RTR server listening on 127.0.0.1 port
3323 and HTTP on port 9556:

.. code-block:: bash

   apt install rsync build-essential
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source ~/.cargo/env
   cargo install --locked routinator
   routinator init
   # Follow instructions provided
   routinator server --rtr 192.0.2.13:3323 --http 192.0.2.13:9556

If you have an older version of Rust and Routinator, you can update via:

.. code-block:: bash

   rustup update
   cargo install --locked --force routinator

If you want to try the master branch from the repository instead of a
release version, you can run:

.. code-block:: bash

   cargo install --git https://github.com/NLnetLabs/routinator.git

Quick Start with Docker
-----------------------

Due to the impracticality of complying with the ARIN TAL distribution terms
in an unsupervised Docker environment, before launching the container it
is necessary to first review and agree to the `ARIN Relying Party Agreement
(RPA) <https://www.arin.net/resources/manage/rpki/tal/>`_. If you
agree to the terms, you can let the Routinator Docker image install the TALs
into a mounted volume that is later reused for the server:

.. code-block:: bash

   # Create a Docker volume to persist TALs in
   sudo docker volume create routinator-tals
   # Review the ARIN terms.
   # Run a disposable container to install TALs.
   sudo docker run --rm -v routinator-tals:/home/routinator/.rpki-cache/tals \
       nlnetlabs/routinator init -f --accept-arin-rpa
   # Launch the final detached container named 'routinator' exposing RTR on
   # port 3323 and HTTP on port 9556
   sudo docker run -d --restart=unless-stopped --name routinator -p 3323:3323 \
        -p 9556:9556 -v routinator-tals:/home/routinator/.rpki-cache/tals \
        nlnetlabs/routinator

System Requirements
-------------------

At this time, the size of the global RPKI data set is about 500MB. Cryptographic
validation of it takes Routinator about 2 seconds on a quad-core i7.

When choosing a system to run Routinator on, make sure you have 1GB of
available memory and 1GB of disk space. This will give you ample margin for
the RPKI repositories to grow over time, as adoption increases.

Getting Started
---------------

There are three things you need to install and run Routinator: rsync, a C
toolchain and Rust. You can install Routinator on any system where you can
fulfil these requirements.

You need rsync because most RPKI repositories currently use it as its main
means of distribution. Some of the cryptographic primitives used by
Routinator require a C toolchain. Lastly, you need Rust because that’s the
programming language that Routinator has been written in.

rsync
"""""

Currently, Routinator requires the :command:`rsync` executable to be in your path.
Due to the nature of rsync, it is unclear which particular version you need at
the very least, but whatever is being shipped with current Linux and \*BSD
distributions and macOS should be fine. Alternatively, you can download rsync
from `its website <https://rsync.samba.org/>`_.

On Windows, Routinator requires the :command:`rsync` version that comes with
`Cygwin <https://www.cygwin.com/>`_ – make sure to select rsync during the
installation phase.

C Toolchain
"""""""""""

Some of the libraries Routinator depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. For example,
:command:`apt install build-essential` will install everything you need on
Debian/Ubuntu.

If you are unsure, try to run :command:`cc` on a command line and if there’s a
complaint about missing input files, you are probably good to go.

Rust
""""

The Rust compiler runs on, and compiles to, a great number of platforms,
though not all of them are equally supported. The official `Rust
Platform Support <https://forge.rust-lang.org/platform-support.html>`_
page provides an overview of the various support levels.

While some system distributions include Rust as system packages,
Routinator relies on a relatively new version of Rust, currently 1.34 or
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

Building
--------

The easiest way to get Routinator is to leave it to cargo by saying:

.. code-block:: bash

   cargo install --locked routinator

If you want to try the master branch from the repository instead of a
release version, you can run:

.. code-block:: bash

   cargo install --git https://github.com/NLnetLabs/routinator.git

If you want to update an installed version, you run the same command but
add the ``-f`` flag, a.k.a. force, to approve overwriting the installed
version.

The command will build Routinator and install it in the same directory
that cargo itself lives in, likely ``$HOME/.cargo/bin``. This means
Routinator will be in your path, too.

Notes
-----

In case you want to build a statically linked Routinator, or you have an
Operating System where special care needs to be taken, such as OpenBSD and
CentOS, please refer to the :ref:`doc_routinator_installation_notes` section.
