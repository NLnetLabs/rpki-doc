.. _doc_ripe_validator_installation:

Installation
------------

We are not distributing binary packages just yet, but getting started with Routinator is really easy. There are three things you need: rsync, Rust and a C toolchain. You need rsync because the RPKI repository currently uses rsync as its main means of distribution. You need Rust because that’s what the Routinator has been written in. Some of the cryptographic primitives used by the Routinator require a C toolchain, so you need that, too. 

You can run Routinator on a UNIX-like operating system in just a couple of steps. Assuming you have rsync and the C toolchain but not yet Rust, here’s how you get the the application to run as an RTR server listening on 127.0.0.1 port 3323:
::

   curl https://sh.rustup.rs -sSf | sh
   source ~/.cargo/env
   cargo install routinator
   routinator rtrd -l 127.0.0.1:3323