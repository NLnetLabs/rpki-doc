.. _doc_rpki_relying_party:

Using RPKI Data
===============

Operators who want to use RPKI data in their BGP decision making process have to fetch and validate all of the published data. As with any Public Key Infrastructure, you have to start with one or more entities you are prepared to trust. In the case of RPKI, these are the five RIRs. When you want to retrieve all RPKI data, you connect to the trust anchor that each of them provides. 

Relying Party software, also known as an RPKI Validator, will start retrieval at each of the RIR Trust Anchors and follow the certificate tree in order to fetch all published certificates and ROAs. Any object that doesn't pass cryptographic validation will be discarded. All remaining information will result in a list of AS Numbers, IP prefixes and maximum prefix length, known as the validated cache. 

The data from the validated cache can be fed directly into RPKI-capable routers via the RPKI-RTR protocol. Many routers, including Cisco, Juniper, Nokia, as well as BIRD and OpenBGPD support processing an RPKI validated cache. Alternatively, Relying Party software can export the validated cache in various useful formats for processing outside of the router in order to set up filters.

When comparing the validated cache to all route announcements seen in BGP, it will have an effect on their RPKI validity. They can be:

- Valid: The route announcement is covered by at least one ROA
- Invalid: The prefix is announced from an unauthorised AS or the announcement is more specific than is allowed by the maximum length set in a ROA that matches the prefix and AS
- NotFound: The prefix in this announcement is not, or only partially covered by an existing ROA

Based on these outcomes, operators can make an informed decision what to do with the BGP route announcements they see. For RPKI to succeed in its objective, operators should at least drop all announcements that are RPKI INVALID.

.. figure:: img/rpki-validation.*
    :align: center
    :width: 100%
    :alt: The RPKI Data Retrieval and Validation

    RPKI publication, data retrieval, validation and processing