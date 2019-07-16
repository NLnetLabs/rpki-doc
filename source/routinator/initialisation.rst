.. _doc_routinator_initialisation:

Initialisation
==============

Before running Routinator for the first time, you must prepare its working environment.
You do this using the ``init`` command. This will prepare both the directory for the
local RPKI cache, as well as the Trust Anchor Locator (TAL) directory. 

By default, both directories will be located under ``$HOME/.rpki-cache``, but you can
change their locations via the command line options ``--repository-dir`` and
``--tal-dir``.

TALs provide hints for the trust anchor certificates to be used both to
discover and validate all RPKI content. The five TALs — one for each Regional
Internet Registry (RIR) — are bundled with Routinator and installed by the
``init`` command.

.. WARNING:: Using the TAL from ARIN, the RIR for the United States, Canada as well as
             many Caribbean and North Atlantic islands, requires you to read and
             accept their `Relying Party Agreement
             <https://www.arin.net/resources/manage/rpki/tal/>`_ before you can use it.
             Running the ``init`` command will provide you with instructions.

.. code-block:: text

   routinator init
   Before we can install the ARIN TAL, you must have read
   and agree to the ARIN Relying Party Agreement (RPA).
   It is available at
   
   https://www.arin.net/resources/manage/rpki/rpa.pdf
   
   If you agree to the RPA, please run the command
   again with the --accept-arin-rpa option.

Running the ``init`` command with the ``--accept-arin-rpa`` option will create the TAL
directory and copy the five Trust Anchor Locator files into it. 

.. code-block:: bash

   routinator init --accept-arin-rpa

If you decide you cannot agree to the ARIN RPA terms, the ``--decline-arin-rpa`` option
will install all TALs except the one for ARIN. If, at a later point, you wish to use the
ARIN TAL anyway, you can add it to your current installation using the ``-f`` flag, to
force the installation of all TALs.

Performing a Test Run
---------------------

To see if Routinator has been initialised correctly and your firewall allows the
required connections, it is recommended to perform an initial test run. You can do this
by having Routinator print a validated ROA payload (VRP) list with the ``vrps``
sub-command, and using ``-v`` to increase the log level to ``INFO`` to see if Routinator
establishes rsync connections as expected.

.. code-block:: bash

   routinator -v vrps

Now, you can see how Routinator connects to the RPKI trust anchors, downloads
the the contents of the repositories to your machine, validates it and produces a 
list of validated ROA payloads in the default CSV format to standard output. From a
cold start, this process will take about two minutes.

.. code-block:: text

   routinator -v vrps
   rsyncing from rsync://repository.lacnic.net/rpki/.
   rsyncing from rsync://rpki.afrinic.net/repository/.
   rsyncing from rsync://rpki.apnic.net/repository/.
   rsyncing from rsync://rpki.ripe.net/ta/.
   rsync://rpki.ripe.net/ta: The RIPE NCC Certification Repository is subject to Terms and Conditions
   rsync://rpki.ripe.net/ta: See http://www.ripe.net/lir-services/ncc/legal/certification/repository-tc
   rsync://rpki.ripe.net/ta: 
   Found valid trust anchor rsync://rpki.ripe.net/ta/ripe-ncc-ta.cer. Processing.
   rsyncing from rsync://rpki.ripe.net/repository/.
   Found valid trust anchor rsync://rpki.afrinic.net/repository/AfriNIC.cer. Processing.
   rsyncing from rsync://rpki.arin.net/repository/.
   Found valid trust anchor rsync://rpki.arin.net/repository/arin-rpki-ta.cer. Processing.
   Found valid trust anchor rsync://rpki.apnic.net/repository/apnic-rpki-root-iana-origin.cer. Processing.
   rsyncing from rsync://rpki.apnic.net/member_repository/.
   Found valid trust anchor rsync://repository.lacnic.net/rpki/lacnic/rta-lacnic-rpki.cer. Processing.
   rsync://rpki.ripe.net/repository: The RIPE NCC Certification Repository is subject to Terms and Conditions
   rsync://rpki.ripe.net/repository: See http://www.ripe.net/lir-services/ncc/legal/certification/repository-tc
   rsync://rpki.ripe.net/repository: 
   rsyncing from rsync://rpkica.twnic.tw/rpki/.
   rsyncing from rsync://rpki-repository.nic.ad.jp/ap/.
   rsyncing from rsync://rpki.cnnic.cn/rpki/.
   Summary:
   afrinic: 338 valid ROAs, 459 VRPs.
   lacnic: 2435 valid ROAs, 7042 VRPs.
   apnic: 3186 valid ROAs, 21934 VRPs.
   ripe: 10780 valid ROAs, 56907 VRPs.
   arin: 4964 valid ROAs, 6621 VRPs.
   ASN,IP Prefix,Max Length,Trust Anchor
   AS43289,2a03:f80:373::/48,48,ripe
   AS14464,131.109.128.0/17,17,arin
   AS17806,114.130.5.0/24,24,apnic
   AS59587,151.232.192.0/21,21,ripe
   AS13335,172.68.30.0/24,24,arin
   AS6147,190.40.0.0/14,24,lacnic
   ...