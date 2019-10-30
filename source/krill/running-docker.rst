.. _doc_krill_running_docker:

Running Krill with Docker
=========================

This page explains the additional features and differences compared to running 
Krill with Cargo that you need to be aware of when running Krill with Docker.

Read :ref:`doc_krill_xrunning` before reading this page.

Get Docker
----------

If you do not already have Docker installed, follow the platform specific
installation instructions via the links in the Docker offical `"Supported platforms" documentation <https://docs.docker.com/install/#supported-platforms>`_.

Fetching and running Krill
--------------------------

The ``docker run`` command will automatically fetch the Krill image the first
time you use it, and so there is no installation step in the traditional sense.

The ``docker run`` command can take `many arguments <https://docs.docker.com/engine/reference/run/>`_
and can be a bit overwhelming at first.

The command below runs Krill in the background and shows how to configure a few
extra things like log level and volume mounts (more on this below).

.. code-block:: bash

   $ docker run -d --name krill -p 127.0.0.1:3000:3000 \
     -e KRILL_LOG_LEVEL=debug \
     -e KRILL_FQDN=some.domain \
     -e KRILL_AUTH_TOKEN=5tT8I7ygoDh9k8I \
     -v krill_data:/var/krill/data/ \
     -v /tmp/krill_rsync/:/var/krill/data/repo/rsync/ \
     nlnetlabs/krill:v0.2.0

Admin Token
-----------

By default Docker Krill secures itself with an automatically generated admin
token. You will need to obtain this token from the Docker logs in order to
manage Krill via the API or the ``krillc`` CLI tool.

.. code-block:: bash

    $ docker logs krill 2>&1 | fgrep token
    docker-krill: Securing Krill daemon with token <SOME_TOKEN>

You can pre-configure the token via the ``auth_token`` Krill config
file setting, or if you don't want to provide a config file you can
also use the Docker environment variable ``KRILL_AUTH_TOKEN`` as 
shown above.

Running the Krill CLI
---------------------

Local
"""""

Using a Bash alias with ``<SOME_TOKEN>`` you can easily interact with the
locally running Krill daemon via its command-line interface (CLI):

.. code-block:: bash

    $ alias krillc='docker exec \
      -e KRILL_CLI_SERVER=https://127.0.0.1:3000/ \
      -e KRILL_CLI_TOKEN=<SOME_TOKEN> \
      krill krillc'

    $ krillc list -f json
    {
      "cas": []
    }

Remote
""""""

The Docker image can also be used to run ``krillc`` to manage remote
Krill servers. Using a shell alias simplifies this considerably:

.. code-block:: bash

    $ alias krillc='docker run --rm \
      -e KRILL_CLI_SERVER=https://some.domain/ \
      -e KRILL_CLI_TOKEN=<SOME_TOKEN> \
      -v /tmp/ka:/tmp/ka nlnetlabs/krill:v0.2.0 krillc'

   $ krillc list -f json
   {
      "cas": []
}

Note: The ``-v`` volume mount is optional, but without it you will not be able
to pass files to ``krillc`` which some subcommands require, e.g.

.. code-block:: bash

   $ krillc roas update --ca my_ca --delta /tmp/delta.in

Proxy and HTTPS
---------------

As advised in :ref:`doc_krill_xrunning` you should run Krill behind an
industry standard proxy server such as nginx.

Service and Certificate URIs
""""""""""""""""""""""""""""

The Krill ``service_uri`` and ``rsync_base`` config file settings can be
configured via the Docker environment variable ``KRILL_FQDN`` as shown in
the example above. Providing ``KRILL_FQDN`` will set **both** ``service_uri``
and ``rsync_base``.

Data
----

Krill writes state and data files to a data directory which in Docker Krill is
hidden inside the Docker container and is lost when the Docker container is
destroyed.

Persistence
"""""""""""

To protect the data you can write it to a persistent `Docker volume <https://docs.docker.com/storage/volumes/>`_
which is preserved even if the Krill Docker container is destroyed. The
following fragment from the example above shows how to configure this:

.. code-block:: bash

   docker run -v krill_data:/var/krill/data/

Access
""""""

Some of the data files written by Krill to its data directory are intended to
be shared with external clients via the rsync protocol. To make this possible
with Docker Krill you can either:

* Mount the rsync data directory in the host and run rsyncd on the host, *OR*
* Share the rsync data with another `Docker container which runs rsyncd <https://hub.docker.com/search?q=rsyncd&type=image>`_

Mounting the data in a host directory:

.. code-block:: bash

   docker run -v /tmp/krill_rsync:/var/krill/data/repo/rsync

Sharing via a named volume:

.. code-block:: bash

   docker run -v krill_rsync:/var/krill/data/repo/rsync

Logging
-------

Krill logs to a file by default. Docker Krill however logs by default
to stderr so that you can see the output using the ``docker logs`` command.

At the default ``warn`` log level Krill doesn't output anything unless there is
something to warn about. Docker Krill however comes with some additional
logging which appears with the prefix ``docker-krill:``. On startup you will
see something like the following in the logs:

.. code-block:: bash

   docker-krill: Securing Krill daemon with token ba473bac-021c-4fc9-9946-6ec109befec3
   docker-krill: Configuring /var/krill/data/krill.conf ..
   docker-krill: Dumping /var/krill/data/krill.conf config file
   ...
   docker-krill: End of dump

Docker Krill environment variable reference
-------------------------------------------

The Krill Docker image supports the following Docker environment variables
which map to the following ``krill.conf`` settings:

+----------------------+------------------------------------+
| Environment variable | Equivalent Krill config setting    |
+======================+====================================+
| ``KRILL_AUTH_TOKEN`` | ``auth_token``                     |
+----------------------+------------------------------------+
| ``KRILL_FQDN``       | ``service_uri`` and ``rsync_base`` |
+----------------------+------------------------------------+
| ``KRILL_LOG_LEVEL``  | ``log_level``                      |
+----------------------+------------------------------------+
| ``KRILL_USE_TA``     | ``use_ta``                         |
+----------------------+------------------------------------+

To set these environment variables use ``-e`` when invoking ``docker``, e.g.:

.. code-block:: bash

   docker run -e KRILL_FQDN=https://some.domain/

Using a config file
-------------------

Via a volume mount you can replace the Docker Krill config file with your 
own and take complete control:

.. code-block:: bash

   docker run -v /tmp/krill.conf:/var/krill/data/krill.conf
   
This will instruct Docker to replace the default config file used by Docker
Krill with the file ``/tmp/krill.conf`` on your host computer.
