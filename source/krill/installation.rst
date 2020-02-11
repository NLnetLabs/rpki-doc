.. _doc_krill_installation:

Installation
============

System Requirements
-------------------

The system requirements for itself are Krill are minimal. The cryptographic
operations that need to be performed by the Krill CA have a negligible
performance and memory impact on any modern day machine.

When you publish ROAs yourself using the Krill publication server in combination
with Rsyncd and a webserver of your choice, you will see traffic from several
hundred relying party software tools querying every few minutes. The total
amount of traffic is also negligible for any modern day situation.

In terms of cryptographic security, you should make sure that the machine is
well protected but a Hardware Security Module (HSM) to store the keys is
certainly not necessary and at this point not even possible.

.. Note:: For reference, NLnet Labs runs Krill and serves ROAs to the world
          using the smallest Virtual Machine they could find (2 CPU / 2GB RAM /
          60GB disk).

Quick Start with Docker
-----------------------

The Docker container for Krill allows you to use it just as you would with Cargo
but without needing to build from sources first.

To fetch and run Krill:

.. code-block:: bash

    docker run --name krill -p 127.0.0.1:3000:3000 nlnetlabs/krill:v0.4.2

With a shell alias interacting with Krill via ``krillc`` is then as
easy as:

.. code-block:: bash

    $ alias krillc='docker exec \
      -e KRILL_CLI_SERVER=https://127.0.0.1:3000/ \
      -e KRILL_CLI_TOKEN=<SOME_TOKEN> \
      krill krillc'

    $ krillc list -f json
    {
      "cas": []
    }

To get the most out of Krill you will want to run Docker and Krill with
additional arguments. See :ref:`doc_krill_xrunning` and
:ref:`doc_krill_running_docker` for more information.


Installing with Cargo
---------------------

There are three things you need for Krill: Rust, a C toolchain and OpenSSL.
You can install the Krill on any Operating System where you can fulfil these
requirements, but we will assume that you will run this on a UNIX-like OS.

Rust
""""

While some system distributions include Rust as system packages,
Krill relies on a relatively new version of Rust, currently 1.30 or
newer. We therefore suggest to use the canonical Rust installation via a
tool called ``rustup``.

To install ``rustup`` and Rust, simply do:

.. code-block:: bash

   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Alternatively, get the file, have a look and then run it manually.
Follow the instructions to get rustup and cargo, the rust build tool, into
your path.

You can update your Rust installation later by simply running:

.. code-block:: bash

   rustup update

To get started you need Cargo's bin directory ($HOME/.cargo/bin) in your PATH
environment variable. To configure your current shell, run

.. code-block:: bash

   source $HOME/.cargo/env

C Toolchain
"""""""""""

Some of the libraries Krill depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. For example,
``apt install build-essential`` will install everything you need on
Debian/Ubuntu.

If you are unsure, try to run ``cc`` on a command line and if there’s a
complaint about missing input files, you are probably good to go.

OpenSSL
"""""""
Your system will likely have a package manager that will allow you to
install OpenSSL in a few easy steps. For Krill, you will need ``libssl-dev``,
sometimes called ``openssl-dev``. On Debian-like Linux distributions,
this should be as simple as running:

.. code-block:: bash

    sudo apt install -y libssl-dev openssl pkg-config

Note: we use Ubuntu Xenial (16.04.5 LTS) in our Travis CI environment.

On macOS you can use Homebrew or MacPorts to get started.


Building
--------

The easiest way to get Krill is to clone the repository and build it using
cargo:

.. code-block:: bash

    git clone git@github.com:NLnetLabs/krill.git --branch v0.4.2 --depth 1
    cd krill

Now you can build the Krill binaries from the Rust source:

.. code-block:: bash

    cargo build --release

This will build the following binaries:

.. code-block:: bash

   target/release/krill
   target/release/krillc

You can copy these binaries to a location of your convenience or run them from
this directory.
