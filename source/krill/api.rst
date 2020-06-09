.. _doc_krill_using_api:

Using the API
=============

The Krill API is a primarily JSON based REST-like HTTPS API with `bearer token
<https://swagger.io/docs/specification/authentication/bearer-authentication/>`_
based authentication.

Getting Help
------------

- Consult the `Interactive API documentation <http://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/NLnetLabs/krill/v0.6.2/doc/openapi.yaml>`_ (courtesy of `ReDoc <https://github.com/Redocly/redoc>`_)
- Follow the API links in the :ref:`Krill CLI documentation<doc_krill_using_cli>`, e.g. API Call: :krill_api:`GET /v1/cas <list_cas>`
- Check out the API hints built-in to the :ref:`Krill CLI<doc_krill_using_cli>`, e.g.

.. parsed-literal::

   $ :ref:`krillc list<cmd_krillc_list>` --api
   GET:
     https://<your.domain>/api/v1/cas
   Headers:
     Authorization: Bearer \*\*\*\*\*


Generating Client Code
----------------------

The `OpenAPI Generator <https://openapi-generator.tech/>`_ can generate Krill
API client code in many languages from the `Krill v0.6.2 OpenAPI specification <https://github.com/NLnetLabs/krill/blob/v0.6.2/doc/openapi.yaml>`_.

Sample Application
------------------

Below is an example of how to write a small Krill client application in Python 3
using a Krill API client library produced by the OpenAPI Generator. To try out
this sample you'll need Docker and Python 3.

1. Save the following as :file:`/tmp/krill_test.py`, replacing `<YOUR XXX>`
values with the correct access token and domain name for your Krill server. This
example assumes that your Krill instance API endpoint is available on port 443
using a valid TLS certificate.

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
   print(krill_ca_api.list_cas())

2. Run the following commands in a shell to generate a Krill client library:

.. code-block:: bash

   # prepare a working directory
   GENDIR=/tmp/gen
   VENVDIR=/tmp/venv
   mkdir -p $GENDIR

   # fetch the Krill OpenAPI specification document
   wget -O $GENDIR/openapi.yaml https://raw.githubusercontent.com/NLnetLabs/krill/v0.6.2/doc/openapi.yaml

   # use the OpenAPI Generator to generate a Krill client library from the krill
   # OpenAPI specification
   docker run --rm -v $GENDIR:/local \
       openapitools/openapi-generator-cli generate \
       -i /local/openapi.yaml \
       -g python \
       -o /local/out \
       --additional-properties=packageName=krill_api

   # install the generated library where your Python 3 can find it
   python3 -m venv $VENVDIR
   source $VENVDIR/bin/activate
   pip3 install wheel
   pip3 install $GENDIR/out/

3. Run the sample application:

.. code-block:: bash

   $ python3 /tmp/krill_test.py
   {'cas': [{'handle': 'ca'}]}

.. Tip:: To learn more about using the generated client library, consult the
         documentation in `$GENDIR/out/README.md`.

.. Warning::

   Future improvements to the Krill OpenAPI specification may necessitate that
   you re-generate your client library and possibly also alter your client
   program to match any changed class and function names.
