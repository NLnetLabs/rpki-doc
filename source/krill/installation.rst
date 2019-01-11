.. _doc_krill_installation:

Installation
============

At this point in time, and until a basic Certificate Authority is implemented,
running Krill is interesting mostly for developers. This means the 
instructions are somewhat developer centric.

We will do proper packaging and a Docker image in the future, but for now you
will need to check out the source code and compile a binary locally.

Getting Started
---------------

There are three things you need for Krill: Rust, a C toolchain and OpenSSL.
You can install the Krill on any Operating System where you can fulfil these
requirements, but will will assume that you will run this on a UNIX-like OS.

Rust
""""

While some system distributions include Rust as system packages,
Krill relies on a relatively new version of Rust, currently 1.30 or
newer. We therefore suggest to use the canonical Rust installation via a
tool called ``rustup``.

To install ``rustup`` and Rust, simply do:

.. code-block:: bash

   curl https://sh.rustup.rs -sSf | sh

Alternatively, get the file, have a look and then run it manually.
Follow the instructions to get rustup and cargo, the rust build tool, into
your path.

You can update your Rust installation later by simply running:

.. code-block:: bash

   rustup update


C Toolchain
"""""""""""

Some of the libraries Krill depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. If you are unsure, try to run
``cc`` on a command line and if there’s a complaint about missing input
files, you are probably good to go.

OpenSSL
"""""""
Your system will likely have a package manager that will allow you to
install OpenSSL in a few easy steps. On most Linux distributions, this
should be as easy as running:

.. code-block:: bash

    sudo apt-get install openssl

On macOS you can use Homebrew or MacPorts to get started.

Building
--------

The easiest way to get Krill is to clone the repository and build it using
cargo:

.. code-block:: bash

    git clone git@github.com:NLnetLabs/krill.git
    cd $project
    cargo build

The package will contain three brinaries: ``krilld``, ``krillc`` and ``pubc``.
Choose the executable to start using the ``--bin`` flag, e.g.:

.. code-block:: bash

    cargo run --bin krilld
