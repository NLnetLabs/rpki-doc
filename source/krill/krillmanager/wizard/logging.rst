.. _doc_krill_manager_wizard_logging:

Wizard: Logging
===============

.. seealso::
     - `Amazon S3 product details <https://aws.amazon.com/s3/>`_ & `Getting your security credentials <https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html>`_
     - `DigitalOcean Spaces product details <https://www.digitalocean.com/products/spaces/>`_ & `AWS S3 Compatibility <https://developers.digitalocean.com/documentation/spaces/#aws-s3-compatibility>`_
     - `Wikipedia: S3 API and competing services <https://en.wikipedia.org/w/index.php?title=Amazon_S3&section=7#S3_API_and_competing_services>`_
     - :ref:`doc_krill_manager_logging` in Krill Manager

If you have an account with a 3rd party S3-like service such as `DigitalOcean
Spaces <https://www.digitalocean.com/products/spaces/>`_, Krill Manager can use
it to store copies of the logs from your host operating systemd journal and the
various Krill Manager operated services, including Krill RFC exchange logs.

.. code-block:: text

  KRILL SETUP WIZARD: Logging             [next: Determining public IP address]
  -----------------------------------------------------------------------------

  Would you like logs (e.g. Krill logs and RFC protocol messages, NGINX and
  RsyncD access and error logs, and operating system logs) to be uploaded
  automatically to an AWS S3 compatible provider (e.g. DigitalOcean Spaces)?

  For information about these services and to sign up and create a
  storage "bucket" please visit the service provider web pages, e.g.:
    - https://aws.amazon.com/s3/
    - https://www.digitalocean.com/products/spaces/

  > Would you like to upload logs to an AWS S3 compatible service? [YES/NO]:

Enter:
  - ``NO`` to skip this page and continue with the wizard.
  - ``YES`` to provide your S3-like service connection details.

.. Warning:: If you do not choose to upload logs they are still available to
             you but in the event that the host suffers a failure you will lose
             these logs unless you capture them as part of a periodic backup
             process.

.. Tip:: You can re-run ``krillmanager init`` later to enable log upload.
         However, note that only new logs from that moment on will be uploaded.

Providing Connection Details
----------------------------

After answering ``YES`` you will be prompted to enter the S3-like service
connection and authentication details. You will need to obtain these from your
S3-like service service provider.

The wizard will try to detect the environment that it is running in and provide
sensible default values where possible, e.g. in the example below the
``S3 Endpoint`` value was set by the wizard based on the fact that the Droplet
on which Krill Manager was running was located in the DigitalOcean ``ams3``
region.

.. code-block:: text

  > Would you like to upload logs to an AWS S3 compatible service? [YES/NO]: YES

  Please provide the connection details for your bucket.

  You may find the official documentation for your AWS S3 provider helpful, e.g.:
    - https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html
    - https://developers.digitalocean.com/documentation/spaces/#aws-s3-compatibility

  > Bucket Name: krillmanagerdemo
  > Bucket Directory: logs
  > S3 Endpoint: ams3.digitaloceanspaces.com
  > Access Key: ********************
  > Secret Key: *******************************************

  Attempting to list contents of bucket krillmanagerdemo to verify credentials..
  Invoking S3 client..
  Success!

  Press any key to continue:

Once the wizard has the connection and authentication details it will attempt
to verify them by trying to list the contents of the destination S3 bucket.

In the event that the connection and/or authentication details are incorrect the
wizard will output error messages instead of ``Success!`` and you will be
returned to the initial yes/no question where you can either choose to try
again or continue without log uploading at this time.

Advanced Configuration
----------------------

For more information about what is logged, how, to where and how to configure
the logging setup beyond what is possible with the wizard, consult the Krill
Manager :ref:`doc_krill_manager_logging` documentation.
