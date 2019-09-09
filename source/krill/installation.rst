.. _doc_krill_installation:

Installation
============

At this point in time, and until a basic Certificate Authority is implemented,
running Krill is interesting mostly for developers. This means the 
instructions are somewhat developer centric. We will do proper packaging and a 
Docker image in the future, but for now you will need to check out the source code
and compile a binary locally.

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
install OpenSSL in a few easy steps. For Krill, you will need ``libssl-dev``,
sometimes called ``openssl-dev``. On debian like Linux distributions, 
this should be as simple as running:

.. code-block:: bash

    sudo apt-get install -y libssl-dev

Note: we use Ubuntu xenial (16.04.5 LTS) in our Travis CI environment.

On macOS you can use Homebrew or MacPorts to get started.

Nodejs and Yarn
"""""""""""""""

Fear not. There is no need to run nodejs when you run krill!

However, nodejs and yarn are used for development of the Vue.js based
user interface. Vue.js uses small components in development. The stuff
that you find under ``./ui/dev/src``. As part of the build process these
components are used to generate minified HTML, CSS and JS under the 
``./ui/dist`` folder.

So, when you build krill locally you will need some additional packages.
The following works on Ubuntu xenial:

.. code-block:: bash

  curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
  curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
  echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
  sudo apt update
  sudo apt-get install -y nodejs
  sudo apt-get install -y yarn


Note that once we build binary versions you will not need nodejs on your
system. Secondly, for now we have one Krill project that includes both
the UI and the API. We may split the project in future if it turns out
that there are users who do not need the UI, and prefer a more light-weight
version. 


Building
--------

The easiest way to get Krill is to clone the repository and build it using
cargo:

.. code-block:: bash

    git clone git@github.com:NLnetLabs/krill.git
    cd $project
    cargo build --release

If you want to see the tests run, you can use the following cargo option:

.. code-block:: bash

    cargo test


This will build the following binaries:

.. code-block:: bash

   target/release/krilld
   target/release/krill_admin

You can copy these binaries to a location of your convenience of run them from this directory.
