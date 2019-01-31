.. _doc_rpki_relying_party:

Using RPKI Data
===============

Validation is a key part of any public key infrastructure. The value from signing comes with validation, and should always be done by the party relying on the data. If validation is outsourced to a third party, you can never be certain if the data is complete, or tampered with in any way.

Operators who want to deploy route origin validation in their BGP decision making process have to fetch and validate all of the published RPKI data. As with any PKI, you have to start with one or more entities you are prepared to trust. In the case of RPKI, these are the five Regional Internet Registries.

Connecting to the Trust Anchor
------------------------------

When you want to retrieve all RPKI data, you connect to the trust anchor that each RIR provides. The root certificate contains pointers to its children, which contain pointers to their children, and so on. These certificates, and other cryptographic material such as ROAs, can be published in the repository that the RIR provides, or a repository operated by an organisation who either runs delegated RPKI themselves, or hosts a repository as a service. As a person who wants to fetch and validate the data, formally known as a relying party, it is not a concern where data is published. By simply connecting to the trust anchor, the chain of trust is followed automatically.

The RIR trust anchor is found through a static trust anchor locator (TAL), which is a very  simple file that contains a URL to retrieve the trust anchor and a public key to verify its authenticity. The reason the TAL exists is because it's very likely that the contents of the self signed root certificate change, due to resource transfers between RIRs. By using a TAL, the data in the trust anchor can change, without it needing to be redistributed.

Fetching and Verifying
----------------------

Various open source relying party software packages, also known as RPKI validators, are available in order to download, verify and process RPKI data. Please note that most RPKI validators come preinstalled with TALs for all RIRs except the one for ARIN, as they require users to first review and agree to their `Relying Party Agreement <https://www.arin.net/resources/rpki/tal.html>`_.

When the validator runs, it will start retrieval at each of the RIR trust anchors and follow the chain of trust to fetch all published certificates and ROAs. Fetching data is currently done via rsync. RIRs and software developers are gradually migrating to the RPKI Repository Delta Protocol (RRDP) for retrieval, standardised in `RFC 8182 <https://tools.ietf.org/html/rfc8182>`_. This protocol uses HTTPS, which makes development and implementation easier, and opens up possibilities for Content Delivery Networks to participate in serving RPKI data. 

Once the data has been downloaded, the validator will verify the signatures on all objects and output the valid route origins as a list. Each object in this list contains an IP prefix, a maximum length, and an origin AS number. This object is referred to as validated ROA payload (VRP). The collection of VRPs is known as the validated cache.

.. Note:: Objects that do not pass cryptographic verification are discarded. 
          Any statements made about route origins are not considered, as if a ROA 
          was never published. As a result, they will not affect any route
          announcements. 
          
          Please note that objects that do not pass cryptographic verification are
          sometimes referred to as 'invalid ROAs', but we like to avoid
          this term because *validity* is used elsewhere in a different context. 

Fetching and verification of data should be performed periodically, in order to process updates. Though the standards recommend retrieval at least once every 24 hours, current operational practice recommends that processing updates every 30 to 60 minutes is reasonable.

Validating Routes
-----------------

As explained, when comparing VRPs to the route announcements seen in BGP, it will have an effect on their RPKI validity state. They can be:

Valid
   The route announcement is covered by at least one VRP. The term *covered* means that
   the prefix in the route announcement is equal, or more specific than the prefix in the
   VRP.

Invalid
   The prefix is announced from an unauthorised AS, or the announcement is more 
   specific than is allowed by the maxLength set in a VRP that matches the 
   prefix and AS.
   
NotFound
   The prefix in this announcement is not, or only partially covered by a VRP.

Please carefully note the use of the word *validity*. Because RPKI revolves around signing and validating cryptographic objects, it's easy to confuse this term with the validity state of a BGP announcement. As mentioned, it can occur that a ROA doesn't pass cryptographic verification, for example because it expired. As a result, it is discarded and will not affect any BGP announcement. In turn, only a validated ROA payload—sometimes referred to as 'valid ROA'—can make a BGP announcement Valid or Invalid.

Based on the three validity outcomes, operators can make an informed decision what to do with the BGP route announcements they see. As a general guideline, announcements with Valid origins should be preferred over those with NotFound or Invalid origins. Announcements with NotFound origins should be preferred over those with Invalid origins.

As origin validation is deployed incrementally, the amount of IP address space that is covered by a ROA will gradually increase over time. Therefore, accepting the NotFound validity should be done for the foreseeable future. 

.. Important:: **For route origin validation to succeed in its objective, operators should
               drop all BGP announcements that are marked as Invalid.** Before deploying
               origin validation, operators should first analyse the effects of such a
               measure to avoid unintended results. Accepting Invalid announcements and
               merely giving them a lower preference should not be done, as they 
               will still propagate across the Internet, with possible
               harmful effects. 

There may be an operational need to accept certain Invalids temporarily. For example, if an Invalid origin is the result of a misconfigured ROA, you may accept it until the operator in question has resolved the issue. Local overrides, standardised in `RFC 8416 <https://tools.ietf.org/html/rfc8416>`_, can be specified to achieve this. 

Feeding Routers
---------------

The RPKI validated cache can be fed directly into RPKI-capable routers via the RPKI to Router Protocol (RPKI-RTR), described in `RFC 8210 <https://tools.ietf.org/html/rfc8210>`_. Many routers, including Cisco, Juniper, Nokia, as well as BIRD and OpenBGPD support processing the validated cache. Alternatively, most validators can export the cache in various useful formats for processing outside of the router, in order to set up filters.

.. figure:: img/rpki-relying-party-process.*
    :align: center
    :width: 100%
    :alt: The RPKI Data Retrieval and Validation

    RPKI publication, data retrieval, validation and processing

Please keep in mind that the RPKI validator software you run in your network fetches cryptographic material from the outside world. To do this, it needs at least ports 873 and 443 open for rsync and HTTPS, respectively. In most cases, the processed data is fed to a router via RPKI-RTR over a clear channel, as it's running in your local network. There is currently no widespread support for SSH, TLS, or other encryption standards. Please take appropriate care when implementing. Lastly, it is recommended to run multiple validator instances as a failover measure.

In the :ref:`doc_rpki_rtr` section we will look at which routers support route origin validation, and how to get started with each.
