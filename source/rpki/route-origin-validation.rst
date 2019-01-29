.. _doc_rpki_rov:

Now that we've looked at how the RPKI structure is built and understand the basics of Internet routing, we can look at how RPKI can be used to make BGP more secure. RPKI provides a set of building blocks allowing for various levels of protection of the routing system. The initial goal is to provide route origin validation, offering a stepping stone to proving path validation in the future.

Both origin validation and path validation are IETF standards. In addition, there are drafts describing autonomous system provider authorisation, aimed at providing a more lightweight, incremental approach to path validation.

Route Origin Validation
=======================


With Route Origin Validation, operators want to answer the question:

    “Is this particular BGP route announcement authorised by the legitimate holder of the address space?”

Using the RPKI system, the legitimate holder of a block of IP addresses can make an authoritative statement about which Autonomous System (AS) is authorised to originate their prefix in BGP. These statements are called Route Origin Authorisations (ROAs).

A ROA states which Autonomous System (AS) is authorised to originate a certain IP address prefix. In addition, it can determine the maximum length of the prefix that the AS is authorised to advertise. By comparing the BGP announcements to published ROAs, a network operator can decide to accept the announcement, drop it or treat it in any other way they choose.

.. figure:: img/rpki-roa-creation.*
    :align: center
    :width: 100%
    :alt: RPKI ROA Creation

    Each CA can issue Route Origin Authorisations


Route Announcement Validity
---------------------------

When a network operator creates a ROA for a certain combination of origin AS and prefix, this will have an effect on the RPKI validity of one or more route announcements. They can be:

- Valid: The route announcement is covered by at least one ROA
- Invalid: The prefix is announced from an unauthorised AS or the announcement is more specific than is allowed by the maximum length set in a ROA that matches the prefix and AS
- NotFound: The prefix in this announcement is not, or only partially covered by an existing ROA

To understand how more specifics, less specifics and partial overlaps are treated, please refer to `section 2 of RFC 6811 <https://tools.ietf.org/html/rfc6811#section-2>`_.

Path Validation
---------------

Currently, RPKI only provides origin validation. While path validation is a desirable characteristic, the existing RPKI origin validation functionality addresses a large portion of the problem surface. 

For many networks, the most important prefixes can be found one AS hop away (coming from a specific peer, for example), and this is the case for large portions of the Internet from the perspective of a transit provider - entities which are ideally situated to act on RPKI data and accept only valid routes for redistribution. 

Furthermore, the vast majority of route hijacks are unintentional, and are caused by ‘fat-fingering’, where an operator accidently originates a prefix they are not the holder of. 

Origin validation would mitigate most of these problems, offering immediate value of the system. While a malicious party could still take advantage of the lack of path validation, widespread RPKI implementation would make such instances easier to pinpoint and address.

With origin validation being deployed in more and more places, there are several efforts to build upon this to offer out-of-band path validation. Autonomous System Provider Authorisation (ASPA) currently has the most traction in the IETF, defined in these drafts: `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_ and `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_.
