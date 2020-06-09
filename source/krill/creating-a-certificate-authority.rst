.. _doc_krill_creating_a_certificate_authority:

Creating a Certificate Authority
================================

For most users the first step with Krill will be to create a Certificate
Authority. How you create the CA depends on how you prefer to use Krill.

Scenarios
---------

- If using the :ref:`Krill UI<doc_krill_using_ui>`, you will be prompted on
  first use to enter the "handle" to use to create a new CA.

- If using the :ref:`Krill CLI<doc_krill_using_cli>` use the following command
  to create a new CA:

  .. parsed-literal::

     $ :ref:`krillc add<cmd_krillc_add>` --ca <handle>

  .. tip:: After creating your new CA, set the ``KRILL_CLI_MY_CA`` environment
           variable to the name of your new CA to reduce typing when using
           subsequent commands. See the
           :ref:`CLI documentation<doc_krill_using_cli>` for more information.

- If using the :ref:`Krill API<doc_krill_using_api>` you will need to use the
  :krill_api:`POST /v1/cas <add_ca>` endpoint to create a CA.

- If using :ref:`Krill Manager<doc_krill_manager>` you will be prompted during
  :ref:`initial setup<doc_krill_manager_initial_setup>` to enter a handle which
  will Krill Manager will use to create a CA for you.

.. note:: If you intend to run the Krill instance as a dedicated
          :ref:`self-hosted repository<doc_krill_publication_server>`
          you do not need to add a CA if using the CLI, but when using
          the UI or Krill Manager you will be prompted to do so.

Next Steps
----------

Learn how to:
  - :ref:`Register your CA with a parent<doc_krill_registering_with_a_parent>`.
  - :ref:`Register your CA with a repository<doc_krill_remote_publishing>`.
  - Manage Route Origin Authorisations (ROAs):

    - With the :ref:`krillc roas<cmd_krillc_roas>` CLI command.
    - With the :ref:`Krill UI<doc_krill_using_ui>`.