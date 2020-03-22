.. _doc_krill_using_cli:

Using the CLI
=============

Every function of Krill can be controlled from the command line interface (CLI).
The Krill CLI is a wrapper around the :ref:`Krill API<doc_krill_using_api>`
which is based on JSON over HTTPS.

It's convenient to set up the following environment variables so that you can
easily use the Krill CLI on the same machine where Krill is running:

.. code-block:: bash

  export KRILL_CLI_TOKEN="correct-horse-battery-staple"
  export KRILL_CLI_SERVER="https://localhost:3000/"
  export KRILL_CLI_MY_CA="Acme-Corp-Intl"

For your CA name, you can use alphanumeric characters, dashes and underscores,
i.e. ``a-zA-Z0-9_``.

Note that you can use the CLI from another machine, but then you will need to
set up a proxy server in front of Krill and make sure that it has a real TLS
certificate.

To use the CLI you need to invoke :command:`krillc` followed by one or more
subcommands, and some arguments. Help is built-in:

.. code-block:: bash

   krillc help [subcommand..]

The following arguments are expected by most subcommands:

.. code-block:: bash

   krillc subcommand [subcommand..]
          [-s, --server https://<yourhost:port>/ ] \
          [-t, --token <token> ]
          [-f, --format text|json|none]   (default text)
          [-c, --ca <ca_name>]            (for ca specific subcommands)

You can set default values for these arguments in environment variables, to make
it a bit easier to use the CLI:

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

For example:

.. code-block:: bash

  export KRILL_CLI_TOKEN="correct-horse-battery-staple"
  export KRILL_CLI_MY_CA="Acme-Corp-Intl"

If you do use the command line argument equivalents, you will override whatever
value you set in the ENV. Krill will give you a friendly error message if you
did not set the applicable ENV variable, and don't include the command line
argument equivalent.

Setting up Your Certificate Authority
-------------------------------------

After Krill is running and configured, you can set up the Certificate Authority.
This involves the following steps:

- Create your CA
- Retrieve your CA's 'child request'
- Retrieve your CA's 'publisher request'
- Upload the 'publisher request' to your publisher
- Save the 'repository response'
- Update the repository for your CA using the 'repository response'
- Upload the 'child request' to your parent
- Save the 'parent response'
- Add the parent using the 'parent response'

.. code-block:: bash

  # Add CA
  krillc add

  # retrieve your CA's 'child request'
  krillc parents request > child_request.xml

  # retrieve your CA's 'publisher request'
  krillc repo request > publisher_request.xml

Next, upload the publisher request XML file to your publication server provider
and save the response XML file.

.. code-block:: bash

  # update the repository for you CA using the 'repository response'
  krillc repo update remote --rfc8183 repository_response.xml

  # add the parent using the 'parent response'
  krillc parents add remote --parent myparent --rfc8183 ./parent-response.xml

Note that you can use any local name for ``--parent``. This is the name that
Krill will show to you. Similarly, Krill will use your local CA name which you
set in the ```KRILL_CLI_MY_CA`` ENV variable. However, the parent response
includes the names (or handles as they are called in the RFC) by which it refers
to itself, and your CA. Krill will make sure that it uses these names in the
communication with the parent. There is no need for these names to be the same.

Managing Route Origin Authorisations
------------------------------------

Krill lets users create Route Origin Authorisations (ROAs), the signed objects
that state which Autonomous System (AS) is authorised to originate one of your
prefixes, along with the maximum prefix length it may have.

You can update ROAs through the command line by submitting a plain text file
with the following format:

.. code-block:: text

 # Some comment
   # Indented comment

  A: 192.0.2.0/24 => 64496
  A: 2001:db8::/32-48 => 64496   # Add prefix with max length
  R: 198.51.100.0/24 => 64496    # Remove existing authorisation

You can then add this to your CA:

.. code-block:: text

 $ krillc roas update --delta ./roas.txt

If you followed the steps above then you would get an error, because there is no
authorisation for 198.51.100.0/24 => 64496. If you remove the line and submit
again, then you should see no response, and no error.

You can list ROAs in the following way:

.. code-block:: text

  $ krillc roas list
  192.0.2.0/24 => 64496
  2001:db8::/32-48 => 64496

Displaying History
------------------

You can show the history of all the things that happened to your CA using the
:command:`history` command.

.. code-block:: text

  $ krillc history
  id: ca version: 0 details: Initialised with ID key hash: 69ee7ef4dae43cd1dcd9ee65b8a1c7fd0c2499c3
  id: ca version: 1 details: added RFC6492 parent 'ripencc'
  id: ca version: 2 details: added resource class with name '0'
  id: ca version: 3 details: requested certificate for key (hash) 'D5EE85EF047010771547FE3ACFE4316503B8EC6F' under resource class '0'
  id: ca version: 4 details: activating pending key 'D5EE85EF047010771547FE3ACFE4316503B8EC6F' under resource class '0'
  id: ca version: 5 details: added route authorization: '192.0.2.0/24 => 64496'
  id: ca version: 6 details: added route authorization: '2001:db8::/32 => 64496'
