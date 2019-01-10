.. _doc_routinator_installation:

Installation
============

At this time there are no binary packages for the Routinator yet, but getting
started is really easy using either `Cargo
<https://crates.io/crates/routinator>`_, `Docker
<https://hub.docker.com/r/nlnetlabs/routinator/>`_, or building from the
`source <https://github.com/NLnetLabs/routinator>`_. 

Quick Start
-----------

Assuming you have rsync and the C toolchain but not yet Rust, here’s how
you get the Routinator to run as an RTR server listening on 127.0.0.1 port
3323:

.. code-block:: bash

   curl https://sh.rustup.rs -sSf | sh
   source ~/.cargo/env
   cargo install routinator
   routinator rtrd -l 127.0.0.1:3323

If you have an older version of the Routinator, you can update via:

.. code-block:: bash

   cargo install -f routinator


Quick Start with Docker
-----------------------

Due to the impracticality of complying with the ARIN TAL distribution terms
in an unsupervised Docker environment, prior to launching the container it
is necessary to first confirm your acceptance of the `ARIN Relying Party Agreement (RPA) <https://www.arin.net/resources/rpki/tal.html>`_. 

The ARIN TAL file in RFC 7730 format available at that URL will then need to
be downloaded and mounted into the docker container as a replacement for
the dummy arin.tal file that is shipped with Routinator.

.. code-block:: bash

   # Create a local directory for the RPKI cache
   sudo mkdir -p /etc/routinator/tals
   # Fetch the ARIN TAL (after agreeing to the distribution terms as described above)
   sudo wget https://www.arin.net/resources/rpki/arin-rfc7730.tal -P /etc/routinator/tals
   # Launch a detached container named 'routinator' (will listen on 0.0.0.0:3323 and expose that port)
   sudo docker run -d --name routinator -p 3323:3323 -v /etc/routinator/tals/arin-rfc7730.tal:/root/.rpki-cache/tals/arin.tal nlnetlabs/routinator


Getting Started
---------------

There are three things you need for Routinator: rsync, Rust and a C toolchain.
You can install the Routinator on any Operating System where you can fulfil
these requirements, so this includes any UNIX-like OS, as well as Microsoft
Windows.

You need rsync because the RPKI repository currently uses rsync as its main
means of distribution. You need Rust because that’s what the Routinator has
been written in. Some of the cryptographic primitives used by the Routinator
require a C toolchain, so you need that, too.

rsync
"""""

Currently, Routinator requires the ``rsync`` executable to be in your path.
Due to the nature of rsync, it is unclear which particular version you need at
the very least, but whatever is being shipped with current Linux and \*BSD
distributions and macOS should be fine.

On Windows, Routinator requires the ``rsync`` version that comes with
`Cygwin <https://www.cygwin.com/>`_ – make sure to select rsync during the
installation phase. 

If you don’t have rsync, please head to https://rsync.samba.org/

Rust
""""

While some system distributions include Rust as system packages,
Routinator relies on a relatively new version of Rust, currently 1.30 or
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

Some of the libraries Routinator depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. If you are unsure, try to run
``cc`` on a command line and if there’s a complaint about missing input
files, you are probably good to go.

Building and Running
--------------------

The easiest way to get Routinator is to leave it to cargo by saying

.. code-block:: bash

   cargo install routinator

If you want to try the master branch from the repository instead of a
release version, you can run

.. code-block:: bash

   cargo install --git https://github.com/NLnetLabs/routinator.git

If you want to update an installed version, you run the same command but
add the ``-f`` flag, a.k.a. force, to approve overwriting the installed
version.

The command will build Routinator and install it in the same directory
that cargo itself lives in, likely ``$HOME/.cargo/bin``. This means 
Routinator will be in your path, too.

