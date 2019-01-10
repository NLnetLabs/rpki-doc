Routinator
==========

.. figure:: img/Routinator_Wordmark.*
     :align: left
     :width: 350px
        

The Routinator is free, open source RPKI Relying Party software written in the Rust programming language. It is designed to be lightweight and have great portability. This means it can run on any Unix-like operating system, but also works on Microsoft Windows. Due to its lean design, it can run effortlessly on minimalist hardware such as a Raspberry Pi. 

The Routinator connects to the Trust Anchors of the five Regional Internet Registries (RIRs) — APNIC, AFRINIC, ARIN, LACNIC and RIPE NCC — downloads all of the certificates and ROAs in their repositories and validates the signatures. It can feed the validated information to hardware routers supporting Route Origin Validation such as `Juniper <https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html>`_, `Cisco <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/15-s/irg-15-s-book/irg-origin-as.html>`_ and `Nokia <https://infocenter.alcatel-lucent.com/public/7750SR160R4A/index.jsp?topic=%2Fcom.sr.unicast%2Fhtml%2Fbgp.html&cp=22_4_7_2&anchor=d2e5366>`_, as well as serving software solutions like `BIRD <https://bird.network.cz/?get_doc&v=20&f=bird-6.html#ss6.11>`_ and `OpenBGPD <http://openbgpd.org>`_. Alternatively, Routinator can output the validated data in a number of useful formats, such as CSV, JSON and RPSL.

When choosing a system to run Routinator on, please note that the application was designed to have a small footprint. The current version consumes as little as 14MB of RAM and cryptographic validation of the RPKI data set only takes a few seconds on modest hardware. 

If you run into a problem with Routinator or you have a feature request, please `create an issue on Github <https://github.com/NLnetLabs/routinator/issues>`_. We are also happy to accept your pull requests. For general discussion and exchanging operational experiences we provide a `mailing list <https://nlnetlabs.nl/mailman/listinfo/rpki>`_. This is also the place where we will announce releases of the application and updates on the project. 

You can follow the adventures of Routinator on `Twitter <https://twitter.com/routinator3000>`_ and listen to its favourite songs on `Spotify <https://open.spotify.com/user/alex.band/playlist/1DkYwN4e4tq73LGAeUykA1?si=AXNn9GkpQ4a-q5skG1yiYQ>`_.

.. toctree::
   :maxdepth: 2
   :name: toc-routinator

   installation
   configuration
   running
   
.. history
.. authors
.. license