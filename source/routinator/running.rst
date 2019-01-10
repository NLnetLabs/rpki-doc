.. _doc_routinator_running:

Running
=======

There are currently two major functions of the Routinator: printing the list
of valid route origins, also known as Validated ROA Payload (VRP), and
providing the service for routers to access this list via a protocol known as
RPKI-to-Router protocol (RTR).

These and all other functions of Routinator are accessible on the command
line via sub-commands. The commands are:

:vrps:
     Produces a list of Validated ROA Payload
     
:rtrd:
     Starts the RTR server
     
First Launch
------------

.. WARNING:: The Routinator comes pre-installed with the Trust Anchor Locators (TALs) 
             of four out of the five RIRs. The ARIN TAL is not automatically loaded, 
             as users must confirm their acceptance of the `ARIN Relying Party Agreement
             (RPA) <https://www.arin.net/resources/rpki/tal.html>`_. 

To see if the Routinator is configured correctly, it is recommended to have it print
a list of Validated ROA Payload and use ``-v`` to increase the log level:

.. code-block:: bash

   routinator -v vrps

When you run the Routinator for the very first time, it will create
``$HOME/.rpki-cache``, put the Trust Anchor Locators (TALs) of the five RIRs
there, and then complain that ARIN’s TAL is in fact not really there.

Follow the instructions provided and try again. You can also add additional
trust anchors by simply dropping their TAL file in `RFC 7730 
<https://tools.ietf.org/html/rfc7730>`_ format into ``$HOME/.rpki-cache/tals``.

Now, Routinator will rsync the entire RPKI repository to your machine (which
will take a while during the first run), validate it and produce a long list
of AS numbers and prefixes in the default CSV format.


Printing a list of valid route origins
--------------------------------------

The Routinator can print a list of valid route origins in four different formats:

:csv: 
     The list is formatted as lines of comma-separated values of the prefix in
     slash notation, the maximum prefix length, the autonomous system number, 
     and an abbreviation for the trust anchor the entry is derived from. The 
     latter is the name of the TAL file  without the extension *.tal*. This is 
     the default format used if the ``-f`` option is missing.
:json:
      The list is placed into a JSON object with a single  element *roas* which
      contains an array of objects with four elements each: The autonomous system 
      number of  the  network  authorised to originate a prefix in *asn*, the prefix
      in slash notation in *prefix*, the maximum prefix length of the announced route
      in *maxLength*, and the trust anchor from which the authorisation was derived 
      in *ta*. This format is identical to that produced by the RIPE NCC Validator 
      except for different naming of the trust anchor. The Routinator uses the name 
      of the TAL file without the extension *.tal* whereas the RIPE NCC Validator 
      has a dedicated name for each.
:openbgpd:
      Choosing  this format causes the Routinator to produce a *roa-set*
      configuration item for the OpenBGPD configuration.
:rpsl:
      This format produces a list of RPSL objects with the authorisation in the
      fields *route*, *origin*, and *source*. In addition, the fields *descr*,
      *mnt-by*, *created*, and *last-modified*, are present with more or less
      meaningful values.
      
For example, to get a file with with the validated ROA payload in JSON format, run:

.. code-block:: bash

   routinator vrps --format json --output roa.json


Feeding a Router with RPKI-RTR
------------------------------

Routinator supports RPKI-RTR as specified in `RFC 8210 
<https://tools.ietf.org/html/rfc8210>`_ as well as the older version from `RFC 6810 
<https://tools.ietf.org/html/rfc7730>`_. It will act as an RTR server if you start 
it with the ``rtrd`` sub-command. It will do so as a daemon and detach from your
terminal unless you provide the ``-a`` (for attached) option.

You can specify the address(es) to listen on via the ``-l`` (or ``--listen``)
option. If you don’t, it will listen on ``127.0.0.1:3323`` by default. This
isn’t the IANA-assigned default port for the protocol, which would be 323.
But since that is a privileged port you’d need to be running Routinator as
root when otherwise there is no reason to do that. Also, note that the
default address is a localhost address for security reasons.

So, in order to run Routinator as an RTR server listening on port 3323 on
both 192.0.2.13 and 2001:0DB8::13 without detaching from the terminal, run:

.. code-block:: bash

   routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323

By default, the repository will be updated and re-validated every hour as
per the recommendation in the RFC. You can change this via the
``--refresh`` option and specify the interval between re-validations in
seconds. That is, if you rather have Routinator validate every fifteen
minutes, the above command becomes:

.. code-block:: bash

    routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323 --refresh=900
