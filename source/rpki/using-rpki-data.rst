.. _doc_rpki_relying_party:

Using RPKI Data
===============

Operators who want to use RPKI data in their BGP decision making process have to fetch and validate all of the published data. As with any Public Key Infrastructure, you have to start with one or more entities you are prepared to trust. In the case of RPKI, these are the five RIRs. When you want to retrieve all RPKI data, you connect to the trust anchor that each of them provides.

There are various open source relying party software packages, also known as RPKI validators, available in order to download, validate and process RPKI data. Please note that most RPKI validators come preinstalled with trust anchor locators for all RIRs except the one for ARIN, as they require users to first review and agree to their `Relying Party Agreement <https://www.arin.net/resources/rpki/tal.html>`_.

When the validator runs, it will start retrieval at each of the RIR trust anchors and follow the certificate tree in order to fetch all published certificates and ROAs. The validator will then verify the signatures on all objects and output the valid route origins, known as the validated ROA payload (VRP), or validated cache, as a list. 

.. Important:: Objects that do not pass cryptographic validation are discarded. Any
               statements made about route origins are not considered, as if a ROA 
               was never published. As a result, an invalid ROA will not affect any 
               route announcements.

The validated data can be fed directly into RPKI-capable routers via the RPKI-RTR protocol. Many routers, including Cisco, Juniper, Nokia, as well as BIRD and OpenBGPD support processing the payload. Alternatively, most validators can export the payload in various useful formats for processing outside of the router, in order to set up filters.

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
    
