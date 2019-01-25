.. _doc_rpki_introduction:

Introduction
============

.. To understand RPKI and its purpose, there are two main concepts you need to grasp: First, the global allocation of Internet number resources and secondly, the Border Gateway Protocol (BGP), the protocol designed to exchange routing information on the Internet.

To understand RPKI and its purpose, you need to first understand how Internet number resources are allocated globally.


Internet Number Resource Allocation
-----------------------------------

Before being formalised within an organisation, the allocation of Internet number resources, such as IP addresses and Autonomous System (AS) numbers, had been the responsibility of `Jon Postel <https://en.wikipedia.org/wiki/Jon_Postel>`_ at the Information Sciences Institute (ISI) of the University of Southern California (USC). He performed the role of `Internet Assigned Numbers Authority <https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority>`_ (IANA), which is presently a function of the `Internet Corporation for Assigned Names and Numbers <https://en.wikipedia.org/wiki/ICANN>`_ (ICANN).

.. figure:: img/Jon_Postel.jpg
    :align: center
    :width: 100%
    :alt: Jon Postel in 1994, with map of Internet top-level domains

    Jon Postel in 1994, with map of Internet top-level domains

Initially, the IANA function was performed globally, but as the work volume grew due to the expansion of the Internet, `Regional Internet Registries <https://en.wikipedia.org/wiki/Regional_Internet_registry>`_ (RIRs) were established over the years to perform this function on a regional level. This meant that a large block of IP address space (typically a /8 in IPv4) was allocated from IANA to the RIR, who would, in turn, allocate to their member organisations.

Today, there are five RIRs responsible for the allocation and registration of Internet number resources within a particular region of the world:

- The `African Network Information Center <https://www.afrinic.net/>`_ (AFRINIC) serves Africa
- The `American Registry for Internet Numbers <https://www.arin.net/>`_ (ARIN) serves Antarctica, Canada, parts of the Caribbean, and the United States
- The `Asia-Pacific Network Information Centre <https://www.apnic.net/>`_ (APNIC) serves East Asia, Oceania, South Asia, and Southeast Asia
- The `Latin America and Caribbean Network Information Centre <https://www.lacnic.net/>`_ (LACNIC) serves most of the Caribbean and all of Latin America
- The `Réseaux IP Européens Network Coordination Centre <https://www.ripe.net/>`_ (RIPE NCC) serves Europe, the Middle East, Russia, and parts of Central Asia

.. figure:: img/Regional_Internet_Registries_world_map.*
    :align: center
    :width: 100%
    :alt: Map of Regional Internet Registries

    Map of Regional Internet Registries

In the APNIC and LACNIC regions, Internet number resources are allocated to National Internet Registries (NIRs), such as NIC.br in Brazil and JPNIC in Japan, who allocate address space to its members or constituents, which are generally organised at a national level. In the rest of world, the RIRs allocated directly to their member organisations, typically referred to as Local Internet Registries (LIRs). Most LIRs are Internet service providers, enterprises, or academic institutions. LIRs either use the allocated IP address blocks themselves or assign them to End User organisations. 

.. figure:: img/ip-allocation-structure.*
    :align: center
    :width: 100%
    :alt: Internet number resource allocation hierarchy

    Internet number resource allocation hierarchy

In RPKI, resource certificates attest to the allocation by the issuer of IP addresses or AS numbers to the subject. This means IANA has the authoritative registration of resources to the five RIRs. Each RIR registers authoritative information on the allocations to NIRs and LIRs, and lastly LIRs record to which End User organisation they assigned resources.

As a result, the certificate hierarchy in RPKI follows the same structure as the allocation hierarchy, with the exception of the IANA level. IANA does not operate a single root Certificate Authority. Instead, the five RIRs each run a root CA.


The "R" in RPKI stands for "Resource"
"""""""""""""""""""""""""""""""""""""

Because RPKI is used in the BGP routing context, a common misconception is that this is the "Routing" PKI. However, certificates in this PKI are called **resource** certificates and conform to the certificate profile for such certificates, as described in RFC 6487. 

It's important to note that RPKI certificates do not attest to the identity of the subject. Certificates simply do not contain any identity information, this is what the five RIRs have a registry and a public whois database for. Therefore, the subject names used in certificates are not intended to be descriptive, and are nothing more than a hash.

Internet Routing
----------------

The global routing system of the Internet consists of a number of functionally independent actors (Autonomous Systems) which use BGP (Border Gateway Protocol) to exchange routing information. The system is very dynamic and flexible by design. Connectivity and routing topologies are subject to change. Changes easily propagate globally within a few minutes. One weakness of this system is that these changes cannot be validated against information existing outside of the BGP protocol itself.

Resource Public Key Infrastructure (RPKI) is a way to define data in an out-of-band system such that the information that is exchanged by BGP can be validated to be correct. The RPKI standards were developed by the IETF (Internet Engineering Task Force) to describe some of the resources of the Internet’s routing and addressing scheme in a cryptographic system.

RPKI is a community-driven system in which open source software developers, router vendors and all five RIRs participate. Using the RPKI system, the legitimate holder of a block of IP addresses can make an authoritative statement about which Autonomous System (AS) is authorised to originate their prefix in the BGP. In turn, other network operators can download and validate these statements and make routing decisions based on them. This process is referred to as Route Origin Validation (ROV).

Expanding upon the Internet Routing Registry
--------------------------------------------

If you've been involved in default-free zone Internet engineering for any length of time, you're probably familiar with RPSL, a routing policy specification language originally defined in `RFC2280 <https://tools.ietf.org/html/rfc2280>`_ back in 1998. While RPSL has created considerable early enthusiasm and has seen some traction, the Internet was rapidly growing at the time, and the primary focus was on data availability rather than data trustworthiness. Everyone was busy opportunistically documenting the minimal policy that was necessary to "make things work" with the policy specification language parsing scripts of everyone else so that something would finally ping!

Over time, this has created an extensive repository of obsolete data of uncertain validity spread across dozens of route registries around the world. Additionally, the RPSL language and supporting tools have proven to be too complex to consistently transpose policy into router configuration language - resulting in most published RPSL data being neither sufficiently accurate and up to date for filtering purposes, nor sufficiently comprehensive or precise for being the golden master in router configuration.

RPKI aims to complement and expand upon this effort focusing primarily on trustworthiness, timeliness, and accuracy of data. RPKI ROAs are hierarchically delegated by RIRs based on strict criteria, and are cryptographically verifiable. This offers the Internet community an opportunity to build an up to date and accurate information of IP address origination data on the Internet.

In conclusion, the main weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. The result is that out of all the information on routing intent that is published, it is difficult to determine what is legitimate, authentic data and what isn’t. RPKI solves these two problems, as you can be absolutely sure that an authoritative, cryptographically verifiable statement can be made by any legitimate IP resource holder in the world.