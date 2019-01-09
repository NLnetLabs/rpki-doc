.. _doc_rpki_rov:

Expanding upon the Internet Routing Registry
--------------------------------------------

If you've been involved in default-free zone Internet engineering for any length of time, you're probably familiar with RPSL, a routing policy specification language originally defined in `RFC2280 <https://tools.ietf.org/html/rfc2280>`_ back in 1998. While RPSL has created considerable early enthusiasm and has seen some traction, the Internet was rapidly growing at the time, and the primary focus was on data availability rather than data trustworthiness. Everyone was busy opportunistically documenting the minimal policy that was necessary to "make things work" with the policy specification language parsing scripts of everyone else so that something would finally ping!

Over time, this has created an extensive repository of obsolete data of uncertain validity spread across dozens of route registries around the world. Additionally, the RPSL language and supporting tools have proven to be too complex to consistently transpose policy into router configuration language - resulting in most published RPSL data being neither sufficiently accurate and up to date for filtering purposes, nor sufficiently comprehensive or precise for being the golden master in router configuration.

RPKI aims to complement and expand upon this effort focusing primarily on trustworthiness, timeliness, and accuracy of data. RPKI ROAs are hierarchically delegated by RIRs based on strict criteria, and are cryptographically verifiable. This offers the Internet community an opportunity to build an up to date and accurate information of IP address origination data on the Internet.

In conclusion, the main weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. The result is that out of all the information on routing intent that is published, it is difficult to determine what is legitimate, authentic data and what isn’t. RPKI solves these two problems, as you can be absolutely sure that an authoritative, cryptographically verifiable statement can be made by any legitimate IP resource holder in the world.


Route Origin Validation
-----------------------

Routing errors on the Internet can be classified as route leaks or route hijacks. A route leak is a propagation of one or more routing announcements that are beyond their intended scope. That is an announcement from an Autonomous System (AS) of a learned BGP route to another AS is in violation of the intended policies of the receiver, the sender, and/or one of the ASes along the preceding AS path.

A route hijack is the unauthorised origination of a route. 

Note that in either case, the cause may be accidental or malicious and in either case, the result can be path detours, redirection, or denial of services. For more information, please refer to `RFC 7908 <https://tools.ietf.org/html/rfc7908>`_.

With Route Origin Validation, operators want to answer the question:

    “*Is this particular BGP route announcement authorised by the legitimate holder of the address space?*”

Using the RPKI system, the legitimate holder of a block of IP addresses can make an authoritative statement about which Autonomous System (AS) is authorised to originate their prefix in the BGP. These statements are called Route Origin Authorisations (ROAs).

A ROA states which Autonomous System (AS) is authorised to originate a certain IP address prefix. In addition, it can determine the maximum length of the prefix that the AS is authorised to advertise. By comparing the BGP announcements to published ROAs, a network operator can decide to accept the announcement, drop it or treat it in any other way they choose.

Route Announcement Validity
+++++++++++++++++++++++++++

When a network operator creates a ROA for a certain combination of origin AS and prefix, this will have an effect on the RPKI validity of one or more route announcements. They can be:

- Valid: The route announcement is covered by at least one ROA
- Invalid: The prefix is announced from an unauthorised AS or the announcement is more specific than is allowed by the maximum length set in a ROA that matches the prefix and AS
- NotFound: The prefix in this announcement is not, or only partially covered by an existing ROA

To understand how more specifics, less specifics and partial overlaps are treated, please refer to `section 2 of RFC 6811 <https://tools.ietf.org/html/rfc6811#section-2>`_.

Path Validation
+++++++++++++++

Currently, RPKI only provides origin validation. While path validation is a desirable characteristic, the existing RPKI origin validation functionality addresses a large portion of the problem surface. 

For many networks, the most important prefixes can be found one AS hop away (coming from a specific peer, for example), and this is the case for large portions of the Internet from the perspective of a transit provider - entities which are ideally situated to act on RPKI data and accept only valid routes for redistribution. 

Furthermore, the vast majority of route hijacks are unintentional, and are caused by ‘fat-fingering’, where an operator accidently originates a prefix they are not the holder of. 

Origin validation would mitigate most of these problems, offering immediate value of the system. While a malicious party could still take advantage of the lack of path validation, widespread RPKI implementation would make such instances easier to pinpoint and address.

With origin validation being deployed in more and more places, there are several efforts to build upon this to offer out-of-band path validation. Autonomous System Provider Authorisation (ASPA) currently has the most traction in the IETF, defined in these drafts: `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_ and `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_.
