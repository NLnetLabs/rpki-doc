.. _doc_faq:

FAQ
+++

.. note::  This is a community-driven FAQ for RPKI, originally written by Alex Band, 
           Job Snijders, David Monosov and Melchior Aelmans. Network operators 
           around the world have contributed to these questions and answers. The 
           contents are available on `Github <https://github.com/NLnetLabs/rpki-faq>`_,
           allowing you to send a pull request with edits or additions, or fork the
           contents for usage elsewhere.

RPKI Mechanism
==============

What is RPKI and why was it developed?
--------------------------------------

The global routing system of the Internet consists of a number of functionally independent actors (Autonomous Systems) which use BGP (Border Gateway Protocol) to exchange routing information. The system is very dynamic and flexible by design. Connectivity and routing topologies are subject to change. Changes easily propagate globally within a few minutes. One weakness of this system is that these changes cannot be validated against information existing outside of the BGP protocol itself.

RPKI is a way to define data in an out-of-band system such that the information that are exchanged by BGP can be validated to be correct. The RPKI standards were developed by the IETF (Internet Engineering Task Force) to describe some of the resources of the Internet’s routing and addressing scheme in a cryptographic system. These information are public, and anyone can get access to validate their integrity using cryptographic methods.

I thought we were all using the IRR to check route origin, why do we need RPKI now?
-----------------------------------------------------------------------------------

If you've been involved in default-free zone Internet engineering for any length of time, you're probably familiar with RPSL, a routing policy specification language originally defined in `RFC2280 <https://tools.ietf.org/html/rfc2280>`_ back in 1998. While RPSL has created considerable early enthusiasm and has seen some traction, the Internet was rapidly growing at the time, and the primary focus was on data availability rather than data trustworthiness. Everyone was busy opportunistically documenting the minimal policy that was necessary to "make things work" with the policy specification language parsing scripts of everyone else so that something would finally ping!

Over time, this has created an extensive repository of obsolete data of uncertain validity spread across dozens of route registries around the world. Additionally, the RPSL language and supporting tools have proven to be too complex to consistently transpose policy into router configuration language - resulting in most published RPSL data being neither sufficiently accurate and up to date for filtering purposes, nor sufficiently comprehensive or precise for being the golden master in router configuration.

RPKI aims to complement and expand upon this effort focusing primarily on trustworthiness, timeliness, and accuracy of data. RPKI ROAs are hierarchically delegated by RIRs based on strict criteria, and are cryptographically verifiable. This offers the Internet community an opportunity to build an up to date and accurate information of IP address origination data on the Internet.

Why are we investing in RPKI, isn’t it easier to just fix the Internet Routing Registry (IRR) system?
-----------------------------------------------------------------------------------------------------

The main weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. The result is that out of all the information on routing intent that is published, it is difficult to determine what is legitimate, authentic data and what isn’t. RPKI solves these two problems, as you can be absolutely sure that an authoritative, cryptographically verifiable statement can be made by any legitimate IP resource holder in the world.

Is it true that BGP4 is just not up to the task any longer?
-----------------------------------------------------------

Unfortunately it’s practically impossible to replace BGP right now. We should, however, work on fixing the broken parts and improving the situation.

As RPKI relies on X.509 PKI, isn’t this the same problem with untrustworthy SSL/TLS Certificate Authorities all over again?
---------------------------------------------------------------------------------------------------------------------------

Instead of relying on a large number of CAs subject to variable auditing standards which come pre-installed in a browser or an operating system, RPKI relies on just five Trust Anchors, run by the Regional Internet Registries. 

These are well established and openly governed organisations. Each operator that wishes to get an RPKI resource certificate already has a contractual relationship with one or more of the RIRs.

What is the value of RPKI based BGP Origin Validation without Path Validation?
------------------------------------------------------------------------------

While Path Validation is a desirable characteristic, the existing RPKI origin validation functionality addresses a large portion of the problem surface. 

For many networks, the most important prefixes can be found one AS hop away (coming from a specific peer, for example), and this is the case for large portions of the Internet from the perspective of a transit provider - entities which are ideally situated to act on RPKI data and accept only valid routes for redistribution. 

Furthermore, the vast majority of route hijacks are unintentional, and are caused by ‘fat-fingering’, where an operator accidently originates a prefix they are not the holder of. 

Origin Validation would mitigate most of these problems, offering immediate value of the system. While a malicious party could still take advantage of the lack of path validation, widespread RPKI implementation would make such instances easier to pinpoint and address.

When comparing the ROA data set to the announcements my router sees, what are possible outcomes?
------------------------------------------------------------------------------------------------

In short, routes can have the state Valid, Invalid, or NotFound (a.k.a. Unknown).

- Valid: The route announcement is covered by at least one ROA
- Invalid: The prefix is announced from an unauthorised AS or the announcement is more specific than is allowed by the maximum length set in a ROA that matches the prefix and AS
- NotFound: The prefix in this announcement is not covered (or only partially covered) by an existing ROA

To understand how more specifics, less specifics and partial overlaps are treated, please refer to `section 2 of RFC 6811 <https://tools.ietf.org/html/rfc6811#section-2>`_.

I’ve heard the term "route leak" and "route hijack". What’s the difference?
---------------------------------------------------------------------------

A route leak is a propagation of one or more routing announcements that are beyond their intended scope. That is an announcement from an Autonomous System (AS) of a learned BGP route to another AS is in violation of the intended policies of the receiver, the sender, and/or one of the ASes along the preceding AS path.

A route hijack is the unauthorised origination of a route. 

Note that in either case, the cause may be accidental or malicious and in either case, the result can be path detours, redirection, or denial of services. For more information, please refer to `RFC 7908 <https://tools.ietf.org/html/rfc7908>`_.

If a ROA is cryptographically invalid, will it make my route invalid?
---------------------------------------------------------------------

An invalid ROA means that the object did not pass cryptographic validation and is therefore discarded. The statement about routing that was made within the ROA is simply not taken into consideration. An invalid route on the other hand, is the result of a valid ROA, specifically one that had the outcome that a prefix is announced from an unauthorised AS or the announcement is more specific than is allowed by the maximum length set in a ROA that matches the prefix and AS.

Operations and Impact
=====================

Will my router have a problem with all of this cryptographic validation?
------------------------------------------------------------------------

No, routers do not do any cryptographic operations to perform Route Origin Validation. The signatures are checked by external software, called Relying Party software or RPKI Validator, which feeds the processed data to the router over a light-weight protocol. This architecture causes minimal overhead for routers. 

Does RPKI reduce the BGP convergence speed of my routers?
---------------------------------------------------------

No, filtering based on an RPKI validated cache has a negligible influence on convergence speed. RPKI validation happens in parallel with route learning (for new prefixes which aren’t yet in cache), and those prefixes will be marked as valid, invalid, or notfound (and the correct policy applied) as the information becomes available.

Why do I need rsync on my system to use a validator?
----------------------------------------------------

In the original standards, rsync was defined as the main means of distribution of RPKI data. While it has served the system well in the early years, rsync has several downsides:

- When RPKI relying party software is used on a client system, it has a dependency on rsync. Different versions and different supported options, such as ``--contimeout``, cause unpredictable results. Furthermore, calling rsync is inefficient. It's an additional process and the output can only be verified by scanning the disk.
- Scaling becomes more and more problematic as the global RPKI data set grows and more operators download and validate data, as with rsync the server in involved in processing the differences.

To overcome these limitations the RRDP protocol was developed and standardised in `RFC 8182 <https://tools.ietf.org/html/rfc8182>`_, which relies on HTTPS. RRDP was specifically designed for scaling and allows CDNs to participate in serving the RPKI data set globally, at scale. In addition, HTTPS is well supported in programming languages so development of relying party software becomes easier and more robust.

Currently, RRDP is implemented on the server side by the RIPE NCC and APNIC. It is `considered as a work item <https://www.arin.net/participate/acsp/suggestions/2018-14.html>`_ for 2019 by ARIN. Most RPKI Validator implementations either already have RRDP support, or have it on the short term roadmap.

The five RIRs provide a Hosted RPKI system, so why would I want to run a Delegated RPKI system myself instead?
--------------------------------------------------------------------------------------------------------------

The RPKI system was designed to be a distributed system, allowing each organisation to run their own CA and publish the certificate and ROAs themselves. The hosted RIR systems are in place to offer a low entry barrier into the system, allowing operators to gain operational experience before deciding if they want to run their own CA. 

For many operators, the hosted system will be good enough, also in the long term. However, organisations who for example don’t want to be dependent on a web interface for management, who manage address space across multiple RIR regions, or have BGP automation in place that they would like to integrate with ROA management, can all choose to run a CA on their own systems.

Should I run a validator myself, when I can use an external data source I found on the Internet?
------------------------------------------------------------------------------------------------

The value of signing the authoritative statements about routing intent by the resource holder comes from being able to validate that the data is authentic and has not been tampered with in any way. 

When you outsource the validation to a third party, you lose the certainty of data accuracy and authenticity. Conceptually, this is similar to DNSSEC validation, which is best done by a local trusted resolver.

`Section 3 of RFC 7115 <https://tools.ietf.org/html/rfc7115#section-3>`_ has an extensive section on this specific topic.

How often should I fetch new data from the RPKI repositories?
-------------------------------------------------------------

According to `section 3 of RFC 7115 <https://tools.ietf.org/html/rfc7115#section-3>`_ you should fetch new data at least every 4 to 6 hours. At the moment, the publication of new ROAs in the largest repositories takes about 10-15 minutes. This means fetching every 15-30 minutes is reasonable, without putting unnecessary load on the system. 

What if the RPKI system becomes unavailable or some other catastrophe occurs, will my (signed) prefixes become unreachable to others? Will other prefixes my routers learned over BGP become unreachable for me?
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

RPKI provides a positive statement on routing intent. If all RPKI validator instances become unavailable and all certificates and ROAs expire, the validity state of all routes will fall back to NotFound, as if RPKI were never used. Routes with this state should be accepted according to `section 5 of RFC 7115 <https://tools.ietf.org/html/rfc7115#section-5>`_, as this state will unfortunately be true for the majority of routes. 

What if the Validator I use crashes and my router stops getting a feed. What will happen to the prefixes I learn over BGP?
--------------------------------------------------------------------------------------------------------------------------

All routers that support Route Origin Validation allow you to specify multiple Validators for redundancy. It is recommended that you run multiple instances, preferably from independent publishers and on separate subnets. This way you rely on multiple caches.

In case of a complete failure, all routes will fall back to the NotFound state, as if Origin Validation were never used.  

I don’t want to rely on the RPKI data set in all cases, but I want to have my own preferences for some routes. What can I do?
-----------------------------------------------------------------------------------------------------------------------------

You can always apply your own, local overrides on specific prefixes/announcements and override the RPKI data you fetch from the repositories. Specifying overrides is in fact standardised in `RFC8416 <https://tools.ietf.org/html/rfc8416>`_, “Simplified Local Internet Number Resource Management with the RPKI (SLURM)”.

Is there any point in signing my routes with ROAs if I don’t validate and filter myself?
----------------------------------------------------------------------------------------

Yes, signing your routes is always a good idea. Even if you don’t validate yourself someone else will, or in worst case someone else might try to hijack your prefix. Imagine what could happen if you haven’t signed your prefixes... 

Miscellaneous
=============

What is the global adoption and data quality of RPKI like?
----------------------------------------------------------

There are several initiatives that measure the adoption and data quality of RPKI:

- `Global certificate and ROA statistics <http://certification-stats.ripe.net>`_, by RIPE NCC
- `Global country statistics <https://lirportal.ripe.net/certification/content/static/statistics/world-roas.html>`_, by RIPE NCC
- `Cirrus Certificate Transparency Log <https://ct.cloudflare.com/logs/cirrus>`_, by Cloudflare
- `The RPKI Observatory <https://nusenu.github.io/RPKI-Observatory/>`_, by nusenu
- `RPKI Deployment Monitor <https://rpki-monitor.antd.nist.gov>`_, by NIST

I want to use the RPKI services from a specific RIR that I'm not currently a member of. Can I transfer my resources?
--------------------------------------------------------------------------------------------------------------------

The RPKI services that each RIR offers differ in conditions, terms of service, availability and usability. Most RIRs have a transfer policy that allow their members to transfer their resources from one RIR region to another. Organisations may wish to do this so that they bring all resources under one entity, simplifying management. Others may do this because they are are looking for a specific set of terms with regards to the holdership of their resources. Please check with your RIR for the possibilities and conditions for resource transfers.

Will RPKI be used as a censorship mechanism allowing governments to make arbitrary prefixes unroutable on a whim?
-----------------------------------------------------------------------------------------------------------------

Unlikely. In order to suppress a prefix, it would be necessary to both revoke the existing ROA (if one is present) and publish a conflicting ROA with a different origin. 

These characteristics make using RPKI as a mechanism for censorship a rather convoluted and uncertain way of achieving this goal, and has broad visibility (as the conflicting ROA, as well as the Regional Internet Registry under which it was issued, will be immediately accessible to everyone). A government would be much better off walking into the data center and confiscate your equipment.

What are the long-term plans for RPKI?
--------------------------------------
With RPKI Route Origin Validation being deployed in more and more places, there are several efforts to build upon this to offer out-of-band Path Validation. Autonomous System Provider Authorisation (ASPA) currently has the most traction in the IETF, defined in these drafts: `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_ and `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_.