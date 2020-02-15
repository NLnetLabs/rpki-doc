.. _doc_krill_using_ui:

Using the UI
============

For most use cases, the user interface is the easiest way to use Krill. Creating
a CA, connecting to a Regional or National Internet Registry parent and
publication server, as well as managing ROAs can all be done from the UI.

You can access the user interface in a browser on the server running Krill at ``https://localhost:3000``. By default, Krill generates a self-signed TLS certificate, so you will have to accept the security warning that your browser will give you.

If you want to access the UI from a different computer, you can either set up a reverse proxy on your server running Krill, or set up local port forwarding with SSH from your computer, for example:

.. code-block:: bash

  ssh -L 3000:localhost:3000 user@krill.example.net

Initial Setup
-------------

The first screen will ask you for the secret token you configured for Krill.

.. figure:: img/krill-ui-enter-password.png
    :align: center
    :width: 100%
    :alt: Krill password screen

    Enter your secret token to access Krill

Next, you will see the Welcome screen when you can create your Certificate
Authority. It will be used to configure Delegated RPKI with one or multiple
parent CAs, usually your Regional or National Internet Registry.

The handle you select is not published in the RPKI but used as identification to
parent and child CAs you interact with. Please choose a handle that helps others
recognise your organisation. Once set, the handle cannot be changed.

.. figure:: img/krill-ui-welome.png
    :align: center
    :width: 100%
    :alt: Krill welcome screen

    Enter a handle for your Certificate Authority
