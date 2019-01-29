.. _doc_rpki_rov:

Now that we've looked at how the RPKI structure is built and understand the basics of Internet routing, we can look at how RPKI can be used to make BGP more secure. RPKI provides a set of building blocks allowing for various levels of protection of the routing system. The initial goal is to provide route origin validation, offering a stepping stone to providing path validation in the future.

Both origin validation and path validation are documented IETF standards. In addition, there are drafts describing autonomous system provider authorisation, aimed at providing a more lightweight, incremental approach to path validation.

Route Origin Validation
=======================

With route origin validation (ROV), the RPKI system tries to closely mimic what *route* objects in the IRR intend to do, but then in a more trustworthy manner. It also adds a couple of useful features.

Origin validation is currently the only functionality that is operationally used. The five RIRs provide functionality for it, there is open source software available for creation and publication of data, and all major router vendors have implemented ROV in their platforms. Various router software implementations offer support for it, as well. 

Route Origin Authorisations
---------------------------

Using the RPKI system, the legitimate holder of a block of IP addresses can make an authoritative, signed statement about which autonomous system is authorised to originate their prefix in BGP. These statements are called Route Origin Authorisations (ROAs).

.. figure:: img/rpki-roa-creation.*
    :align: center
    :width: 100%
    :alt: RPKI ROA Creation

    Each CA can issue Route Origin Authorisations

The creation of a ROA is solely tied to the IP address space that is listed on the certificate and not to the AS numbers. This means the holder of the certificate can authorise any AS to originate their prefix, not just their own autonomous systems. 

Maximum Prefix Length
"""""""""""""""""""""

In addition to the origin AS and the prefix, the ROA contains a maximum length (maxLength) value. This is an attribute that a *route* object in RPSL doesn't have. Described in `RFC 6482 <https://tools.ietf.org/html/rfc6482>`_, the maxLength specifies the maximum length of the IP address prefix that the AS is authorised to advertise. This gives the holder of the prefix control over the level of deaggregation an AS is allowed to do. 

For example, if a ROA authorises a certain AS to originate 192.0.1.0/24 and the maxLength is set to /25, the AS can originate a single /24 or two adjacent /25 blocks. Any more specific announcement is unauthorised by the ROA. Using this example, the shorthand notation for prefix and maxLength you will often encounter is ``192.0.1.0/24-25``.

.. WARNING:: According to `RFC 7115 <https://tools.ietf.org/html/rfc7115>`_, operators
             should be conservative in use of maxLength in ROAs. For
             example, if a prefix will have only a few sub-prefixes announced,
             multiple ROAs for the specific announcements should be used as
             opposed to one ROA with a long maxLength. 
             
             **Liberal usage of maxLength opens up the network to a forged origin
             attack. ROAs should be as precise as possible, meaning they should 
             match prefixes as announced in BGP.**

In a forged origin attack, a malicious actor spoofs the AS number of another network. With a minimal ROA length, the attack does not work for sub-prefixes that are not covered by overly long maxLength. For example, if, instead of 10.0.0.0/16-24, one issues 10.0.0.0/16 and 10.0.42.0/24, a forged origin attack cannot succeed against 10.0.666.0/24. They must attack the whole /16, which is more likely to be noticed because of its size.

Route Announcement Validity
---------------------------

When a network operator creates a ROA for a certain combination of origin AS and prefix, this will have an effect on the RPKI validity of one or more route announcements. `RFC 6811 <https://tools.ietf.org/html/rfc6811>`_ describes the possible statuses, which are:

Valid
   The route announcement is covered by at least one ROA.

Invalid
   The prefix is announced from an unauthorised AS, or the announcement is more 
   specific than is allowed by the maxLength set in a ROA that matches the 
   prefix and AS.
   
NotFound
   The prefix in this announcement is not, or only partially covered by an existing ROA.

Anyone can download and validate the published certificates and ROAs and make routing decisions based on these three outcomes. As a general guideline, announcements with Valid origins should be preferred over those with NotFound or Invalid origins. Announcements with NotFound origins should be preferred over those with Invalid origins.

As origin validation is deployed incrementally, the amount of IP address space that is covered by a ROA will gradually increase over time. Therefore, accepting the NotFound validity should be done for the foreseeable future. 

For route origin validation to succeed in its objective, operators should drop all announcements that are marked as Invalid. Before deploying origin validation, operators should first analyse the effects of such a measure to avoid unintended results. Accepting Invalid announcements and merely giving them a lower preference should not be done, as the announcement will still propagate across the Internet, with possible harmful effects. 

There may be an operational need to accept certain Invalids temporarily. For example, if an Invalid origin is the result of a misconfigured ROA, you may accept it until the operator in question has resolved the issue. Local overrides, standardised in `RFC 8416 <https://tools.ietf.org/html/rfc8416>`_, can be specified to achieve this. 

Path Validation
===============

Currently, RPKI only provides origin validation. While BGPsec path validation is a desirable characteristic and standardised in `RFC 8205 <https://tools.ietf.org/html/rfc8205>`_, real-world deployment may prove limited for the foreseeable future. However, RPKI origin validation functionality addresses a large portion of the problem surface. 

For many networks, the most important prefixes can be found one AS hop away (coming from a specific peer, for example), and this is the case for large portions of the Internet from the perspective of a transit provider - entities which are ideally situated to act on RPKI data and accept only valid routes for redistribution. 

Furthermore, the vast majority of route hijacks are unintentional, and are caused by ‘fat-fingering’, where an operator accidently originates a prefix they are not the holder of. 

Origin validation would mitigate most of these problems, offering immediate value of the system. While a malicious party could still take advantage of the lack of path validation, widespread RPKI implementation would make such instances easier to pinpoint and address.

With origin validation being deployed in more and more places, there are several efforts to build upon this to offer out-of-band path validation. Autonomous System Provider Authorisation (ASPA) currently has the most traction in the IETF, defined in these drafts: `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_ and `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_.
