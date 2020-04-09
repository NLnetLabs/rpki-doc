.. _doc_krill_manager_wizard_https_certificates:

Wizard: HTTPS Certificates
==========================

.. seealso::
     - :ref:`proxy_and_https`
     - `Let's Encrypt HTTP-01 challenge <https://letsencrypt.org/docs/challenge-types/#http-01-challenge>`_

As advised in :ref:`proxy_and_https`, Krill Manager makes Krill available to
the Internet via the NGINX proxy server. When running in self-publishing mode
(see :ref:`doc_krill_manager_wizard_publication_mode`) NGINX is also used to
offer the RRDP protocol to Relying Party clients.

To secure the connection NGINX requires a TLS certificate, either provided by
you or requested on your behalf from `Let's Encrypt <https://letsencrypt.org/>`_
by the Krill Manager wizard.

For each domain that requires a TLS certificate (either just one domain for
Krill, or if using a separate domain for RRDP then that domain too) the wizard
checks whether or not it already has a certificate and asks how you would like
to proceed:

.. code-block:: text

  KRILL SETUP WIZARD: HTTPS certificates              [next: Verifying domains]
  -----------------------------------------------------------------------------

  Domain: ca.demo.krill.cloud

  Checking for certificate files for domain:
    Certificate: Not found
    Private key: Not found

  An HTTPS certificate and corresponding private key file are required for
  this domain.

  How would you like to proceed? Enter one of:
    - NEW: to request (or renew) a Lets Encrypt certificate, OR
    - OWN: to supply your own certificate files from /tmp, OR

  > NEW or OWN:

Enter one of:
  - ``NEW`` to request a new `Let's Encrypt <https://letsencrypt.org/>`_ certificate.
  - ``OWN`` to supply your own certificate files.
  - ``USE`` to use the existing certificate that the wizard found, if any.

Using Let's Encrypt Certificates
--------------------------------

When using Let's Encrypt issued certificates Krill Manager will ensure that
they are renewed before they expire.

.. Warning:: When using your own certificates, instead of Krill Manager
             obtained Let's Encrypt certificates, you are responsible for
             replacing the certificate files before the certificates expire.

DNS and Firewall Requirements
"""""""""""""""""""""""""""""

For Let's Encrypt to issue a TLS certificate the following requirements must be
met:

- A DNS A record for the domain name must point to the Krill Manager IP
  address.
- The DNS A record must have sufficiently propagated around the global DNS
  network such that multiple Let's Encrypt probe locations around the world
  can all resolve the name correctly.
- Port 80 on the Krill Manager instance must be open, both on the host and
  on any cloud firewall or proxy layer (e.g. load balancer) in front of
  the Krill Manager instance.

IP Address Verification
"""""""""""""""""""""""

Prior to requesting a Let's Encrypt certificate the wizard will ask you to
confirm that DNS lookup results for the domain look correct.

.. code-block:: text

  KRILL SETUP WIZARD: HTTPS certificates              [next: Applying settings]
  -----------------------------------------------------------------------------

  Domain: ca.demo.krill.cloud

  To respond to the Lets Encrypt HTTP-01 challenge, a standalone certbot web
  server will be started on this Droplet on port 80.

  info: In order for Lets Encrypt to issue a certificate for this domain there
  must be a DNS A record pointing either to:

    - the IP address of this Droplet: 198.51.100.2, OR
    - the IP address of a proxy such as a load balancer or CDN

  From this Droplet the DNS lookup result for the domain is:
    ca.demo.krill.cloud.	59	IN	A	198.51.100.2


  > Are you sure you want to continue? [YES/NO]:

Let's Encrypt Request Log
"""""""""""""""""""""""""

If you approve the wizard will then contact Let's Encrypt:

.. code-block:: text

  > Are you sure you want to continue? [YES/NO]: YES
  Deleting any existing Lets Encrypt certificate files for this domain
  Deleting any self-signed/provided certificate files for this domain
  Stopping NGINX if running
  Requesting Lets Encrypt certificate for domain demo.krill.cloud
  letsencrypt: Saving debug log to /var/log/letsencrypt/letsencrypt.log
  letsencrypt: Plugins selected: Authenticator standalone, Installer None
  letsencrypt: Registering without email!
  letsencrypt: Obtaining a new certificate
  letsencrypt: Performing the following challenges:
  letsencrypt: http-01 challenge for demo.krill.cloud
  letsencrypt: Waiting for verification...
  letsencrypt: Cleaning up challenges
  letsencrypt: IMPORTANT NOTES:
  letsencrypt:  - Congratulations! Your certificate and chain have been saved at:
  letsencrypt:    /etc/letsencrypt/live/ca.demo.krill.cloud/fullchain.pem
  letsencrypt:    Your key file has been saved at:
  letsencrypt:    /etc/letsencrypt/live/ca.demo.krill.cloud/privkey.pem
  letsencrypt:    Your cert will expire on 2020-07-07. To obtain a new or tweaked
  letsencrypt:    version of this certificate in the future, simply run certbot
  letsencrypt:    again. To non-interactively renew *all* of your certificates, run
  letsencrypt:    "certbot renew"
  letsencrypt:  - Your account credentials have been saved in your Certbot
  letsencrypt:    configuration directory at /etc/letsencrypt. You should make a
  letsencrypt:    secure backup of this folder now. This configuration directory will
  letsencrypt:    also contain certificates and private keys obtained by Certbot so
  letsencrypt:    making regular backups of this folder is ideal.
  letsencrypt:  - If you like Certbot, please consider supporting our work by:
  letsencrypt:    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
  letsencrypt:    Donating to EFF:                    https://eff.org/donate-le

  Press any key to continue:

In this example the request succeeded. If any problems occurred the log would
instead indicate the reason for the failure.

Once you press a key to continue you will be returned to the start of the HTTPS
Certificates wizard page. The wizard will verify if it now has a certificate
for the domain and if so will give you the option to ``USE`` it:

.. code-block:: text

  KRILL SETUP WIZARD: HTTPS certificates              [next: Verifying domains]
  -----------------------------------------------------------------------------

  Domain: ca.demo.krill.cloud

    Checking for certificate files for domain:
      Certificate: Found
      Private key: Found

    This certificate was issued for: subject=CN = ca.demo.krill.cloud
    This certificate was issued by : issuer=C = US, O = Let's Encrypt, CN = Let's Encrypt Authority X3

    How would you like to proceed? Enter one of:
      - USE: Use this certificate, OR
      - NEW: to request (or renew) a Lets Encrypt certificate, OR
      - OWN: to supply your own certificate files from /tmp, OR

    > NEW, OWN, or USE:
