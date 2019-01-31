.. _doc_rpki_relying_party:

Using RPKI Data
===============

Operators who want to use RPKI data in their BGP decision making process have to fetch and validate all of the published data. As with any Public Key Infrastructure, you have to start with one or more entities you are prepared to trust. In the case of RPKI, these are the five RIRs. 

Connecting to the Trust Anchor
------------------------------

When you want to retrieve all RPKI data, you connect to the trust anchor that each RIR provides. The root certificate contains pointers to its children, which contain pointers to their children, and so on. These certificates and other cryptographic material such as ROAs can be published in the repository that the RIR provides, or a repository operated by an organisation who either runs delegated RPKI themselves, or provides a repository as a service. As a person who wants to fetch and validate the data, formally known as a relying party, it is not a concern where data is published. By simply connecting to the trust anchor, the certificate tree is followed automatically.

The RIR trust anchor is found through a static trust anchor locator (TAL), which is a very  simple file that contains a URL to retrieve the trust anchor and a public key to verify its authenticity. The reason the TAL exists is because it's very likely that the contents of the self signed root certificate changes, due to resource transfers between RIRs. By using a TAL, the data in the trust anchor can change, without it needing to be redistributed.

Fetching and Validating
-----------------------

There are various open source relying party software packages, also known as RPKI validators, available in order to download, validate and process RPKI data. Please note that most RPKI validators come preinstalled with TALs for all RIRs except the one for ARIN, as they require users to first review and agree to their `Relying Party Agreement <https://www.arin.net/resources/rpki/tal.html>`_.

When the validator runs, it will start retrieval at each of the RIR trust anchors and follow the certificate tree in order to fetch all published certificates and ROAs. Fetching data is currently done via rsync. The ``rsync`` daemon runs on port 873 by default, so make sure this port is open on the firewall. 

RIRs and software developers a gradually migrating to the RPKI Repository Delta Protocol (RRDP) for fetching data, standardised in `RFC 8182 <https://tools.ietf.org/html/rfc8182>`_. This protocol uses HTTPS, which makes development and implementation easier, and opens up possibilities for Content Delivery Networks to participate in serving RPKI data. 

Once the data has been downloaded, the validator will verify the signatures on all objects and output the valid route origins, known as the validated ROA payload (VRP), or validated cache, as a list.

.. Important:: Objects that do not pass cryptographic validation are discarded. Any
               statements made about route origins are not considered, as if a ROA 
               was never published. As a result, an invalid ROA will not affect any 
               route announcements.

Fetching and validating data should be performed periodically, in order to process changes. How often this should be done depends on how often changes occur and how often new material is published. Though the standards recommend fetching at least once every 24 hours, current operational practice recommends that fetching every 15 to 30 minutes is reasonable.

Processing
----------

The validated data can be fed directly into RPKI-capable routers via the RPKI to Router Protocol (RPKI-RTR), described in `RFC 8210 <https://tools.ietf.org/html/rfc8210>`_. Many routers, including Cisco, Juniper, Nokia, as well as BIRD and OpenBGPD support processing the payload. Alternatively, most validators can export the payload in various useful formats for processing outside of the router, in order to set up filters.

When comparing the VRPs to the route announcements seen in BGP by the configured router, it will have an effect on their RPKI validity. They can be:

Valid
   The route announcement is covered by at least one ROA.

Invalid
   The prefix is announced from an unauthorised AS, or the announcement is more 
   specific than is allowed by the maxLength set in a ROA that matches the 
   prefix and AS.
   
NotFound
   The prefix in this announcement is not, or only partially covered by an existing ROA.

Based on these outcomes, operators can make an informed decision what to do with the BGP route announcements they see. For RPKI to succeed in its objective, operators should at least drop all announcements that are RPKI Invalid.

.. figure:: img/rpki-relying-party-process.*
    :align: center
    :width: 100%
    :alt: The RPKI Data Retrieval and Validation

    RPKI publication, data retrieval, validation and processing
    

