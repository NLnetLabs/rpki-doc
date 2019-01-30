.. _doc_krill_pub_running:

.. WARNING::  This part of the project is currently being built. 
              Documentation will likely change significantly as the software evolves.

Running
=======

Use this command to run the publication server with two example clients:

.. code-block:: bash

   ./target/debug/krilld -c ./data/krill.conf
   
The server should start on localhost and port 3000. If you want to use a 
different configuration, please review the config file (./defaults/krill.conf). 
Alternatively, you can use the ``-c`` option to specify another config file.

Command Line Interface
----------------------

There is a command line interface (CLI) shipping with Krill for Krill admin 
tasks. This CLI provides a simple wrapper around the API. It does not use any
back doors. It may be more convenient that calling the API directly from your
favourite scripting language, but maybe even more importantly: it allows us 
to set up integration and regression testing when building this software.  
 
The binary is built as part of the normal 'cargo build' process, and can be 
used by running:

.. code-block:: bash

   ./target/debug krillc

To get an overview of all supported options run:

.. code-block:: bash

   ./target/debug krillc --help

Which will print something like this:

.. code-block:: bash

	Krill admin client 0.2.0

	USAGE:
		krillc [OPTIONS] --server <URI> --token <token-string> [SUBCOMMAND]

	FLAGS:
		-h, --help       Prints help information
		-V, --version    Prints version information

	OPTIONS:
		-f, --format <type>           Specify the report format (none|json|text|xml). 
		                              If left unspecified the format will match
									  the corresponding server api response type.
		-s, --server <URI>            Specify the full URI to the krill server.
		-t, --token <token-string>    Specify the value of an admin token.

	SUBCOMMANDS:
		health        Perform a health check. Exits with exit code 0 if all is well, 
		              exit code 1 in case of any issues
		help          Prints this message or the help of the given subcommand(s)
		publishers    Manage publishers

