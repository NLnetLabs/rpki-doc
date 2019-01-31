.. _doc_rpki_relying_party:

Using RPKI Data
===============

Operators who want to use RPKI data in their BGP decision making process have to fetch and validate all of the published data. As with any Public Key Infrastructure, you have to start with one or more entities you are prepared to trust. In the case of RPKI, these are the five RIRs. 

Connecting to the Trust Anchor
------------------------------

When you want to retrieve all RPKI data, you connect to the trust anchor that each RIR provides. The root certificate contains pointers to its children, which contain pointers to their children, and so on. These certificates and other cryptographic material such as ROAs can be published in the repository that the RIR provides, or a repository operated by an organisation who either runs delegated RPKI themselves, or provides a repository as a service. As a person who wants to fetch and validate the data, formally known as a relying party, it is not a concern where data is published. By simply connecting to the trust anchor, the certificate tree is followed automatically.

The RIR trust anchor is found through a static trust anchor locator (TAL), which is a very  simple file that contains a URL to retrieve the trust anchor and a public key to verify its authenticity. The reason the TAL exists is because it's very likely that the contents of the self signed root certificate changes, due to resource transfers between RIRs. By using a TAL, the data in the trust anchor can change, without it needing to be redistributed.

Fetching and Verifying
----------------------

There are various open source relying party software packages, also known as RPKI validators, available in order to download, verify and process RPKI data. Please note that most RPKI validators come preinstalled with TALs for all RIRs except the one for ARIN, as they require users to first review and agree to their `Relying Party Agreement <https://www.arin.net/resources/rpki/tal.html>`_.

When the validator runs, it will start retrieval at each of the RIR trust anchors and follow the certificate tree in order to fetch all published certificates and ROAs. Fetching data is currently done via rsync. RIRs and software developers a gradually migrating to the RPKI Repository Delta Protocol (RRDP) for fetching data, standardised in `RFC 8182 <https://tools.ietf.org/html/rfc8182>`_. This protocol uses HTTPS, which makes development and implementation easier, and opens up possibilities for Content Delivery Networks to participate in serving RPKI data. 

Once the data has been downloaded, the validator will verify the signatures on all objects and output the valid route origins as a list. Each object in this list contains an IP prefix, a maximum length, and the origin AS number. This object is referred to as validated ROA payload (VRP). The collection of VRPs is known as the validated cache.

.. Important:: Objects that do not pass cryptographic verification are discarded. 
               Any statements made about route origins are not considered, as if a ROA 
               was never published. As a result, it will not affect any route
               announcements. 
               
               Please note that objects that do not pass cryptographic verification are
               sometimes referred to as 'invalid ROAs', but we like to avoid
               this because *validity* is used elsewhere in a different context. 

Fetching and validating data should be performed periodically, in order to process changes. How often this should be done depends on how often changes occur and how often new material is published. Though the standards recommend fetching at least once every 24 hours, current operational practice recommends that fetching every 30 to 60 minutes is reasonable.

Validating Routes
-----------------

When comparing VRPs to the route announcements seen in BGP by the configured router, it will have an effect on their RPKI validity state. They can be:

Valid
   The route announcement is covered by at least one VRP.

Invalid
   The prefix is announced from an unauthorised AS, or the announcement is more 
   specific than is allowed by the maxLength set in a VRP that matches the 
   prefix and AS.
   
NotFound
   The prefix in this announcement is not, or only partially covered by a VRP.

Please carefully note the use of the word *validity*. Because RPKI revolves around signing and validating cryptographic objects, it's easy to confuse this term with the validity state of a BGP announcement. As mentioned, it can occur that a ROA doesn't pass cryptographic verification, for example because it expired. As a result, it is discarded and will not affect any BGP announcement. In turn, only a validated ROA payload—sometimes referred to as 'valid ROA'— can make a BGP announcement Invalid.

Based on the three validity outcomes, operators can make an informed decision what to do with the BGP route announcements they see. For RPKI to succeed in its objective, operators should at least drop all announcements that are RPKI Invalid.

Feeding Routers
---------------
    
The RPKI validated cache can be fed directly into RPKI-capable routers via the RPKI to Router Protocol (RPKI-RTR), described in `RFC 8210 <https://tools.ietf.org/html/rfc8210>`_. Many routers, including Cisco, Juniper, Nokia, as well as BIRD and OpenBGPD support processing the payload. Alternatively, most validators can export the payload in various useful formats for processing outside of the router, in order to set up filters.

.. figure:: img/rpki-relying-party-process.*
    :align: center
    :width: 100%
    :alt: The RPKI Data Retrieval and Validation

    RPKI publication, data retrieval, validation and processing

Lastly, keep in mind that an RPKI validator is software that fetches data from the outside world and needs open ports for at least rsync (873) and in most cases HTTPS (443) to work. In most cases, the processed data is fed to a router via RPKI-RTR over a clear channel. There is currently no widespread support yet for SSH, TLS, or other encryption standards. Please take appropriate care when implementing.
