.. _doc_routinator_installation:

Installation
============

At this time, there are no binary packages for Routinator yet, but getting
started with Routinator is really easy, using either `Cargo
<https://crates.io/crates/routinator>`_, `Docker
<https://hub.docker.com/r/nlnetlabs/routinator/>`_ or building from the `source
<https://github.com/NLnetLabs/routinator>`_. 

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

If you have an older version of the Routinator, you can update via

.. code-block:: bash

   cargo install -f routinator


Quick Start with Docker
-----------------------

Due to the impracticality of complying with the ARIN TAL distribution terms
in an unsupervised Docker environment, prior to launching the container it
is necessary to first review and agree to the ARIN TAL terms available at
https://www.arin.net/resources/rpki/tal.html

The ARIN TAL RFC 7730 format file available at that URL will then need to
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

There are three things you need for Routinator: rsync, Rust and a C toolchain. You can install the Routinator on any Operating System when you can fulfil these requirements, so this inclused any UNIX-like OS, as well as Microsoft Windows.

You need rsync because the RPKI repository currently uses rsync as its main
means of distribution. You need Rust because that’s what the Routinator has
been written in. Some of the cryptographic primitives used by the Routinator
require a C toolchain, so you need that, too.

### rsync

Currently, Routinator requires the `rsync` executable to be in your path.
We are not quite sure which particular version you need at the very least,
but whatever is being shipped with current Linux and \*BSD distributions
and macOS should be fine.

On Windows, Routinator requires the `rsync` version that comes with
[Cygwin](https://www.cygwin.com/) – make sure to select rsync during the
installation phase. And yes, Routinator totally works on Windows, too.

If you don’t have rsync, please head to http://rsync.samba.org/

### Rust

While some system distributions include Rust as system packages,
Routinator relies on a relatively new version of Rust, currently 1.30 or
newer. We therefore suggest to use the canonical Rust installation via a
tool called *rustup.*

To install *rustup* and Rust, simply do:

```bash
curl https://sh.rustup.rs -sSf | sh
```

or, alternatively, get the file, have a look and then run it manually.
Follow the instructions to get rustup and cargo, the rust build tool, into
your path.

You can update your Rust installation later by simply running

```bash
rustup update
```

### C Toolchain

Some of the libraries Routinator depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. If you are unsure, try to run
`cc` on a command line and if there’s a complaint about missing input
files, you are probably good to go.

## Building and Running

The easiest way to get Routinator is to leave it to cargo by saying

```bash
cargo install routinator
```

If you want to try the master branch from the repository instead of a
release version, you can run

```bash
cargo install --git https://github.com/NLnetLabs/routinator.git
```

If you want to update an installed version, you run the same command but
add the `-f` flag (aka force) to approve overwriting the installed
version.

The command will build Routinator and install it in the same directory
that cargo itself lives in (likely `$HOME/.cargo/bin`).
Which means Routinator will be in your path, too.

There are currently two major functions of the Routinator: printing the
list of valid route origins, also known as _Validated ROA Payload_ or VRP,
and providing the service for routers to access this list via a protocol
known as RPKI-to-Router protocol or RTR.

These (and all other functions) of Routinator are accessible on the
command line via sub-commands. The commands are `vrps` and `rtrd`,
respectively.

So, to have Routinator print the list, you say

```bash
routinator vrps
```

If this is the first time you’ve
been using Routinator, it will create `$HOME/.rpki-cache`, put the
trust anchor locators of the five RIRs there, and then complain that
ARIN’s TAL is in fact not really there.

Follow the instructions provided and try again. You can also add
additional trust anchors by simple dropping their TAL file in RFC 7730
format into `$HOME/.rpki-cache/tals`.

Now Routinator will rsync the entire RPKI repository to your machine
(which will take a while during the first run), validate it and produce
a long list of AS numbers and prefixes.

Information about additional command line arguments is available via the
`-h` option or you can look at the more detailed man page via the `man`
sub-command:

```bash
routinator man
```

It is also available online on the
[NLnetLabs documentation
site](https://www.nlnetlabs.nl/documentation/rpki/routinator/).


## Feeding a Router with RPKI-RTR

Routinator supports RPKI-RTR as specified in RFC 8210 as well as the older
version from RFC 6810. It will act as an RTR server if you start it with
the `rtrd` sub-command. It will do so as a daemon and detach from your
terminal unless you provide the `-a` (for attached) option.

You can specify the address(es) to listen on via the `-l` (or `--listen`)
option. If you don’t, it will listen on `127.0.0.1:3323` by default. This
isn’t the IANA-assigned default port for the protocol, which would be 323.
But since that is a privileged port you’d need to be running Routinator as
root when otherwise there is no reason to do that. Also, note that the
default address is a localhost address for security reasons.

So, in order to run Routinator as an RTR server listening on port 3323 on
both 192.0.2.13 and 2001:0DB8::13 without detaching from the terminal, run

```bash
routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323
```

By default, the repository will be updated and re-validated every hour as
per the recommendation in the RFC. You can change this via the
`--refresh` option and specify the interval between re-validations in
seconds. That is, if you rather have Routinator validate every fifteen
minutes, the above command becomes

```bash
routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323 --refresh=900
```