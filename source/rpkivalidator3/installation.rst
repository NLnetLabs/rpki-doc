.. _doc_rpkivalidator3_installation:

Installation
============

RIPE NCC provides a total of four options for installations:

- :ref:`CentOS (rpm) <rpm>` 
- :ref:`Debian (deb) <deb>`
- :ref:`Generic build (tgz) <tgz>`
- :ref:`Docker image <docker>` 

.. _rpm:

CentOS
------

We have set up a repository with CentOS 7 RPMs for Prod builds. 
You can add the repository to your system as follows:

.. code-block:: bash

   sudo yum-config-manager --add-repo https://ftp.ripe.net/tools/rpki/validator3/prod/centos7/ripencc-rpki-prod.repo


You might have to install 'yum-utils' first:

.. code-block:: bash

    sudo yum install yum-utils


Install the RPKI Validator:

.. code-block:: bash

    sudo yum install rpki-validator


Install the RPKI-RTR Server:

.. code-block:: bash

    sudo yum install rpki-rtr-server

Run and enable the services:

.. code-block:: bash

    sudo systemctl enable rpki-validator-3 
    sudo systemctl start rpki-validator-3

.. code-block:: bash

    sudo systemctl enable rpki-rtr-server 
    sudo systemctl start rpki-rtr-server


To monitor the logs:

.. code-block:: bash

    sudo journalctl -f -u rpki-validator-3 
    sudo journalctl -f -u rpki-rtr-server


The RPKI Validator 3.1 will be running on http://localhost:8080/

The RPKI-RTR Server will be running on http://localhost:8081/

You can also explore the API at http://localhost:8080/swagger-ui.html


.. _deb:

Debian
------

The Debian packages for the RPKI Validator and RPKI-RTR Server can be found at: https://ftp.ripe.net/ripe/tools/rpki/validator3/prod/deb/

Download the suitable package and proceed with the installation:


Install the RPKI Validator:

.. code-block:: bash

    sudo apt install ./rpki-validator-3-latest.deb


Install the RPKI-RTR Server:

.. code-block:: bash

    sudo apt install ./rpki-rtr-server-latest.deb

Run and enable the services:

.. code-block:: bash

    sudo systemctl enable rpki-validator-3 
    sudo systemctl start rpki-validator-3

.. code-block:: bash

    sudo systemctl enable rpki-rtr-server 
    sudo systemctl start rpki-rtr-server


To monitor the logs:

.. code-block:: bash

    sudo journalctl -f -u rpki-validator-3 
    sudo journalctl -f -u rpki-rtr-server


The RPKI Validator 3.1 will be running on http://localhost:8080/

The RPKI-RTR Server will be running on http://localhost:8081/

You can also explore the API at http://localhost:8080/swagger-ui.html


.. _tgz:

Generic build
-------------

You can find generic production builds at: https://ftp.ripe.net/tools/rpki/validator3/prod/generic/
Download the suitable package and unpack it.

To run the RPKI Validator generic build:

.. code-block:: bash

    ./rpki-validator-3.sh


To run the RPKI-RTR generic build:

.. code-block:: bash

    ./rpki-rtr-server.sh


The RPKI Validator 3.1 will be running on http://localhost:8080/

The RPKI-RTR Server will be running on http://localhost:8081/

You can also explore the API at http://localhost:8080/swagger-ui.html


.. _docker:

Docker
------

To run the Centos/RPM based image with systemd:

.. code-block:: bash

    docker pull  ripencc/rpki-validator-3-docker:latest
    docker run --privileged --name rpkival -p 8080:8080 -d ripencc/rpki-validator-3-docker:latest


To run the generic alpine based image:

.. code-block:: bash

    docker pull  ripencc/rpki-validator-3-docker:alpine
    docker run --name validator-3-alpine -p 8080:8080 -d ripencc/rpki-validator-3-docker:alpine


The RPKI Validator 3.1 will be running on: http://localhost:8080/

More info can be found at https://hub.docker.com/r/ripencc/rpki-validator-3-docker



.. _extra-tals:

Extra TALs
------------------

By default, the Validator will have Trust Anchor Locators (TALs) installed for AFRINIC, APNIC, LACNIC, RIPE NCC, but not ARIN.

You can download the ARIN TAL at https://www.arin.net/resources/manage/rpki/tal/

Any of the formats will work, but the "RIPE NCC RPKI Validator format" will ensure that the TAL will have a friendly name like "ARIN".


You can use the following script to upload it:

.. code-block:: bash

    ./upload-tal.sh arin-ripevalidator.tal http://localhost:8080/


The script should be in the root folder if you unpacked the generic build, or in */usr/bin* if you installed it using RPM/Debian package. 

Alternatively, you can put extra TAL files to the preconfigured-tals directory of the RPKI Validator installation. 
This directory is scanned on the start and all the parseable TALs are picked up for validation. 
For the RPM/Debian package installation the directory is */var/lib/rpki-validator-3/preconfigured-tals/*.




