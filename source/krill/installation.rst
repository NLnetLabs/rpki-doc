.. _doc_krill_installation:

Installation
============

At this point in time Krill is still under heavy development, and our main
focus is on functionality, rather that packaging. The structure of the Krill
binaries, as well as configuration files, are still subject to changes.

We will have a Docker image very soon - we have a test version working. Other
than that you will have to check out the source code and compile a binary
locally.


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

To get started you need Cargo's bin directory ($HOME/.cargo/bin) in your PATH 
environment variable. To configure your current shell, run 

.. code-block:: bash

source $HOME/.cargo/env

C Toolchain
"""""""""""

Some of the libraries Krill depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. For example, 
``apt install build-essential`` will install everything you need 
on Debian/Ubuntu.

If you are unsure, try to run ``cc`` on a command line and if there’s a 
complaint about missing input files, you are probably good to go. 

OpenSSL
"""""""
Your system will likely have a package manager that will allow you to
install OpenSSL in a few easy steps. For Krill, you will need ``libssl-dev``,
sometimes called ``openssl-dev``. On debian like Linux distributions, 
this should be as simple as running:

.. code-block:: bash

    sudo apt-get install -y libssl-dev
    sudo apt-get install openssl

Note: we use Ubuntu xenial (16.04.5 LTS) in our Travis CI environment.

On macOS you can use Homebrew or MacPorts to get started.

Nodejs and Yarn
"""""""""""""""

Fear not I. There is no need to run nodejs when you run krill!

Fear not II. We have plans to use vue.js as a static library, and
remove the dependency on Nodejs and Yarn altogether. 

However, for the moment, nodejs and yarn are used for development of
the Vue.js based user interface. Vue.js uses small components in development.
The stuff that you find under ``./ui/dev/src``. As part of the build process these
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
system. 


Building
--------

The easiest way to get Krill is to clone the repository and build it using
cargo:

.. code-block:: bash

    git clone git@github.com:NLnetLabs/krill.git
    cd $project

For now, compile the static HTML pages:

.. code-block:: bash

    daemon/build-dist.sh

Now you can build the krill binaries from the Rust source:

.. code-block:: bash

    cargo build --release


This will build the following binaries:

.. code-block:: bash

   target/release/krilld
   target/release/krill_admin

You can copy these binaries to a location of your convenience of run them from this directory.
