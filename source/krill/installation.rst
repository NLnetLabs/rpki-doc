.. _doc_krill_installation:

Installation
============

You can either build Krill from sources with Cargo or run it using Docker, both
are quite easy.

Quick Start with Docker
-----------------------

To start Krill:

.. code-block:: bash

    docker run -d --name krill -p 127.0.0.1:3000:3000 \
      -e KRILL_LOG_LEVEL=debug \
      -v krill_data:/var/krill/data/ \
      -v /tmp/krill_rsync/:/var/krill/data/repo/rsync/ \
      nlnetlabs/krill:v0.1.0

Now obtain the generated authentication token by running:

.. code-block:: bash

    $ docker logs krill 2>&1 | fgrep token
    docker-krill: Securing Krill daemon with token <SOME_TOKEN>

Using a Bash alias with ``<SOME_TOKEN>`` you can easily interact with the
locally running Krill daemon via its command-line interface (CLI):

.. code-block:: bash

    $ alias ka='docker exec krill krill_admin \
      -s https://127.0.0.1:3000/ \
      -t <SOME_TOKEN>'

    $ ka cas list
    {
      "cas": []
    }

Using Dockerized ``krill_admin`` to manage remote Krill instances:
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

The Docker image can also be used to run ``krill_admin`` without a Krill
server. With the help of a shell alias this becomes quite easy:

.. code-block:: bash

  $ alias ka_remote='docker run --rm \
    -v /tmp/ka:/tmp/ka nlnetlabs/krill:v0.1.0 krill_admin \
    -s https://<some.domain>/ \
    -t <SOME_TOKEN>'

  $ ka_remote cas list

Note: The ``-v`` volume mount is optional, but without it you will not be able
to pass files to ``krill_admin`` which some subcommands require, e.g.

.. code-block:: bash

  $ ka cas roas -h child update -d /tmp/ka/delta.in

Using a HTTPS proxy in front of Dockerized Krill
""""""""""""""""""""""""""""""""""""""""""""""""

Use ``docker run -e KRILL_FQDN=some.domain`` to set the Krill ``service_uri``
and ``rsync_base`` configuration setting. This tells Krill which base URL to
use when generating self references to ensure that clients are directed back to
the HTTPS proxy in front of Krill rather than to Krill itself, as Krill should
not be exposed to insecure networks.

Serving Dockerized Krill data files via rsync
"""""""""""""""""""""""""""""""""""""""""""""

Krill serves generated data files via the RRDP and rsync protocols. The example
invocation of Dockerized Krill above used ``-v /tmp/krill_rsync:/var/krill/data/repo/rsync``
to write rsync files to host directory ``/tmp/krill_rsync``.

Krill does not have its own rsync server. To serve these files via the rsync
protocol you must run an rsync server yourself and configure it to serve these
files. Or you can run an `rsyncd server Docker container<https://hub.docker.com/search?q=rsyncd&type=image>`_
that mounts the Krill rsync data volume via ``-v krill_rsync:/var/krill/data/repo/rsync``.

Further configuration
"""""""""""""""""""""

The Krill Docker image supports the following additional environment variables
which map to the following ``krill.conf`` settings:

- ``KRILL_AUTH_TOKEN`` - sets ``auth_token``.
- ``KRILL_FQDN`` - sets ``service_uri`` and ``rsync_base``.
- ``KRILL_LOG_LEVEL`` - sets ``log_level``.
- ``KRILL_USE_TA`` - sets ``use_ta``.

Providing your own configuration file
"""""""""""""""""""""""""""""""""""""

Provide ``docker run -v /tmp/krill.conf:/var/krill/data/krill.conf`` will
instruct Docker to replace the default config file used by Docker Krill with
the file ``/tmp/krill.conf`` on your host computer.`


Installing with Cargo
---------------------

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
