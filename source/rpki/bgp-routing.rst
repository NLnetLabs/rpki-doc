.. _doc_rpki_bgp_routing:

To understand how RPKI is used to make Internet routing more secure, we must first look at how routing works, what the weaknesses are and which elements RPKI can currently help protect against.

Internet Routing
================

The global routing system of the Internet consists of a number of functionally independent actors called autonomous systems (AS), which use the Border Gateway Protocol (BGP) to exchange routing information. 

An autonomous system is a set of Internet routable IP prefixes belonging to a network or a collection of networks that are all managed and supervised by a single entity or organisation. An AS utilizes a common routing policy controlled by the entity and is identified by a globally unique 16 or 32-bit number. The AS number (ASN) is assigned by one of the five Regional Internet Registries (RIRs), just like IP address blocks.

The Border Gateway Protocol manages the routed peerings, prefix advertisement and routing of packets between different autonomous systems across the Internet. BGP uses the ASN to uniquely identify each system. In short, BGP is the routing protocol for AS paths across the Internet. The system is very dynamic and flexible by design. Connectivity and routing topologies are subject to change, which easily propagate globally within a few minutes. 

Fundamentally, BGP is based on mutual trust between networks. When a network operator configures the routers in their AS, they specify which IP prefixes to originate and announce to their peers. There is not authentication or authorisation embedded within BGP. In principle, an operator can define any ASN as the origin and announce any prefix, also one they are not the holder of. 

Best Path Selection
-------------------

BGP continuously and dynamically makes decisions about the best path is to reach a particular destination. Though there are many factors at play, two of them are the most important to keep in mind within this context.

Preference for Shortest Path
""""""""""""""""""""""""""""

Out of all the possible routes that a router has in its Routing Information Base (RIB), BGP will always prefer the shortest path to its destination, minimising the amount of hops. When two matching prefixes are announced from two different networks on the Internet, BGP will route traffic to the destination that is topologically closest. This is an important feature of of BGP, but when configuration errors occur, it can also be the cause of reachability problems.

.. figure:: img/hijack-shorter-path.*
    :align: center
    :width: 100%
    :alt: When the same prefix is announced, the shortest path wins

    When the announcement of a prefix is an exact match, the shortest path wins

Preference for Most Specific Prefix
"""""""""""""""""""""""""""""""""""

Out of all the possible routes that a router has learned, BGP will always prefer the most specific prefix that is announced, regardless of the path length. Again, this is an important feature, but creates the possibility for almost any network to attract someone else's traffic by announcing an overlapping more specific.

.. figure:: img/hijack-more-specific.*
    :align: center
    :width: 100%
    :alt: A more specific prefix always wins

    Regardless of the path length, the announcement of a more specific prefix always wins
    
With this in mind, there are several problems that can arise as a result of this behavior.

Routing Errors
--------------

Routing errors on the Internet can be classified as route leaks or route hijacks. `RFC 7908 <https://tools.ietf.org/html/rfc7908>`_ provides a working definition of a BGP route leak as: 

   The propagation of routing announcement(s) beyond their intended scope. That is, an
   announcement from an Autonomous System (AS) of a learned BGP route to another AS is 
   in violation of the intended policies of the receiver, the sender, and/or one of the
   ASes along the preceding AS path.

A route hijack, also called prefix hijack, or IP hijack, is unauthorised origination of a route. 

.. note:: Route leaks and hijacks can be accidental or malicious, but most often arise 
          from **accidental misconfigurations**. The result can be redirection of traffic
          through an unintended path. This may enable eavesdropping or traffic analysis
          and may, in some cases, result in a denial of service or black hole.

Mitigation of Routing Errors
----------------------------

One weakness of BGP is that routing errors cannot be easily be deduced from information within the protocol itself. For this reason, network operators have to carefully gauge what the intended routing policy of their peers is. As a result, it is imperative that networks employ filters to only accept legitimate traffic and drop everything else. 

There are several well known methods to achieve this. Certain backbone and private peers require a valid Letter of Agency (LOA) to be completed prior to allowing the announcement or re-announcement of IP address blocks. A more widely accepted method is the usage of the Internet Routing Registry (IRR), where operators can publish their routing policy. This allows other networks to set up filters accordingly.

The Internet Routing Registry
-----------------------------

The Internet Routing Registry (IRR) is a `distributed set of databases <http://www.irr.net/docs/list.html>`_ allowing network operators to describe routing intent. The IRR is used as verification mechanism of route origination and is widely deployed to prevent accidental or intentional routing disturbances. 

The notation used in the IRR is the Routing Policy Specification Language (RPSL), which was originally defined in `RFC 2280 <https://tools.ietf.org/html/rfc2280>`_ in 1998. While RPSL has created considerable early enthusiasm and has seen quite some traction, the Internet was rapidly growing at the time, and the primary focus was on data availability rather than data trustworthiness.

Over time, this has created an extensive repository of obsolete data of uncertain validity, spread across dozens of route registries around the world. Additionally, the RPSL language and supporting tools have proven to be too complex to consistently transpose policy into router configuration language. This resulted in most published RPSL data being neither sufficiently accurate and up to date for filtering purposes, nor sufficiently comprehensive or precise for being the golden master in router configuration.

This leaves the information about route origin as the most valuable attribute of the IRR. In so called *route* objects, operators can specify from which ASN they intend to announce a certain prefix. Based on these objects, other operators can define filters.

In conclusion, the main weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. The result is that out of all the information on routing intent that is published, it is difficult to determine what is legitimate, authentic data and what isn’t. RPKI solves these problems, as you can be absolutely sure that an authoritative, cryptographically verifiable statement can be made by any legitimate IP resource holder in the world. In the next sections we will look at how this is achieved.