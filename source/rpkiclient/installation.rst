.. _doc_rpkiclient_installation:

Installation
============

rpki-client is available for many operating systems and architectures:

- :ref:`OpenBSD` 
- :ref:`Debian (deb) <deb>`
- :ref:`Ubuntu (deb) <deb>`
- :ref:`CentOS (rpm) <rpm>` 
- :ref:`Fedora (rpm) <rpm>` 
- :ref:`FreeBSD (txz) <txz>`
- :ref:`Generic build (tgz) <tgz>`

OpenBSD
-------

No need to install rpki-client, the utility is available as part of the base installation.

Documentation is available here: http://man.openbsd.org/rpki-client

Uncomment the root user's rpki-client crontab entry to run rpki-client every hour.

.. code-block:: bash

    $ doas crontab -l | grep rpki-client
    ~   *   *   *   *   -ns rpki-client -v && bgpctl reload

The crontab arguments ``-ns`` make it so that rpki-client is only emailed to the cron user when there is a problem and prevent multiple concurrent executions.

Validated output is stored in ``/var/db/rpki-client``

.. _rpm:

CentOS / Fedora
---------------

Rpki-client is available through EPEL.

Install epel-release RPM:

.. code-block:: bash

    $ sudo yum install epel-release

Install rpki-client

.. code-block:: bash

    $ sudo yum install rpki-client

TALs are stored in ``/etc/pki/tals/``

.. _deb:

Debian / Ubuntu
---------------

Rpki-client is available as part of the Debian Bullseye (and newer) and Ubuntu 20.10 (and newer) official package repositories.

Install rpki-client:

.. code-block:: bash

    $ sudo apt install rpki-client

And then configure rpki-client to generate its output in the the JSON format needed by GoRTR or RTRTR:

.. code-block:: bash

    echo 'OPTIONS=-j' > /etc/default/rpki-client

You may manually start the service unit to immediately generate the data instead of waiting for the next timer run:

.. code-block:: bash

    $ sudo systemctl start rpki-client &

The validated output will appear in ``/var/lib/rpki-client/``.

TALs are stored in ``/etc/tals/``

.. _txz:

FreeBSD
-------

The FreeBSD packages for rpki-client are part of the official ports collection.

.. code-block:: bash

    # pkg install rpki-client

.. _tgz:

Generic build
-------------

You can find generic source tarballs builds at: http://ftp.openbsd.org/pub/OpenBSD/rpki-client/

Download the latest tarball and unpack it.

To run the configure script

.. code-block:: bash

    $ ./configure

Make the binary

.. code-block:: bash

    $ make

.. _extra-tals:

Extra TALs
----------

By default, rpki-client will have Trust Anchor Locators (TALs) installed for AFRINIC, APNIC, LACNIC, RIPE NCC, but not ARIN.

You can download the ARIN TAL in RFC 7730 format at https://www.arin.net/resources/manage/rpki/tal/

Copy the TAL into the default TAL directory.
