.. _doc_krill_using_api:

Using the API
=============

The Krill API is a primarily JSON based REST-like HTTPS API with `bearer token
<https://swagger.io/docs/specification/authentication/bearer-authentication/>`_
based authentication.

Documentation
-------------

View the `human readable interactive version of the Krill v0.4.2 API
specification
<http://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/NLnetLabs/krill/v0.4.2/doc/openapi.yaml>`_,
made possible by the wonderful `ReDoc <https://github.com/Redocly/redoc>`_ tool.

Specification
-------------

The raw `OpenAPI 3.0.2 specification
<https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md>`_
description of the API is available in the Krill source repository (`v0.4.2 link
<https://github.com/NLnetLabs/krill/blob/v0.4.2/doc/openapi.yaml>`_).

Generating Client Code
----------------------

The `OpenAPI Generator <https://openapi-generator.tech/>`_ can generate client
code for using the krill API with your favourite language. Below is an example
of how to do this using Docker and Python 3.

First create a simple test Krill client program. Save the following as
`/tmp/krill_test.py`, replacing `<YOUR XXX>` values with the correct access
token and domain name for your Krill server.

.. code-block:: python3

   # Import the OpenAPI generated Krill client library
   import krill_api
   from krill_api import *

   # Create a configuration for the client library telling it how to connect to
   # the Krill server
   krill_api_config = krill_api.Configuration()
   krill_api_config.access_token = '<YOUR KRILL API TOKEN>'
   krill_api_config.host = "https://{}/api/v1".format('<YOUR KRILL FQDN>')
   krill_api_config.verify_ssl = True
   krill_api_config.assert_hostname = False
   krill_api_config.cert_file = None

   # Create a Krill API client
   krill_api_client = krill_api.ApiClient(krill_api_config)

   # Get the client helper for the Certificate Authority set of Krill API endpoints
   krill_ca_api = CertificateAuthoritiesApi(krill_api_client)

   # Query Krill for the list of configured CAs
   print(krill_ca_api.cas_get())

Now generate the Krill client library:

.. code-block:: bash

   GENDIR=/tmp/gen
   VENVDIR=/tmp/venv

   mkdir -p $GENDIR

   wget -O $GENDIR/openapi.yaml https://raw.githubusercontent.com/NLnetLabs/krill/v0.4.2/doc/openapi.yaml

   docker run --rm -v $GENDIR:/local \
       openapitools/openapi-generator-cli generate \
       -i /local/openapi.yaml \
       -g python \
       -o /local/out \
       --additional-properties=packageName=krill_api

   python3 -m venv $VENVDIR
   source $VENVDIR/bin/activate
   pip3 install wheel
   pip3 install $GENDIR/out/

And then run the sample client program:

.. code-block:: bash

   python3 /tmp/krill_test.py

To learn more about using your OpenAPI generated client library consult the
`README.md` file that is created in the generated client library directory, e.g.
`$GENDIR/out/README.md` in the example above.

.. warning::

   Future improvements to the Krill OpenAPI specification may necessitate that
   you re-generate your client library and possibly also alter your client
   program to match any changed class and function names.
