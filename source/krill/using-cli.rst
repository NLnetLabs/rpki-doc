Using the Krill CLI
===================

The Krill CLI is a wrapper around the :ref:`Krill API<doc_krill_using_api>`
which is based on JSON over HTTPS. To use the CLI you need to invoke `krillc`
followed by one or more subcommands, and some arguments. Help is built-in:

.. code-block:: bash

   krillc [subcommand..] help

The following arguments are expected by most subcommands:

.. code-block:: bash

   krillc subcommand [subcommand..]
          [-s, --server https://<yourhost:port>/ ] \
          [-t, --token <token> ]
          [-f, --format text|json|none]   (default text)
          [-c, --ca <ca_name>]            (for ca specific subcommands)

You can set default values for these arguments in ENV variables, to make it a
bit easier/concise to use the CLI:

+---------------------+------------------------------------------------------+
| Variable            | Argument                                             |
+---------------------+------------------------------------------------------+
| KRILL_CLI_SERVER    | -s, --server                                         |
+---------------------+------------------------------------------------------+
| KRILL_CLI_TOKEN     | -t, --token                                          |
+---------------------+------------------------------------------------------+
| KRILL_CLI_FORMAT    | -f, --format (defaults to text)                      |
+---------------------+------------------------------------------------------+
| KRILL_CLI_MY_CA     | -c, --ca                                             |
+---------------------+------------------------------------------------------+

If you do use the command line argument equivalents, you will override whatever
value you set in the ENV. Krill will give you a friendly error message if you
did not set the applicable ENV variable, and don't include the command line
argument equivalent.

We will explain the use of various subcommands in the section where we talk
about managing your CA(s) in Krill.
