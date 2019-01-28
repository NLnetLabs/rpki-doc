.. _doc_rpki_bgp_routing:

To understand how RPKI is used to make Internet routing more secure, we must first look at how routing works, what the weaknesses are and which elements RPKI can currently help protect against.

Internet Routing
================

The global routing system of the Internet consists of a number of functionally independent actors called autonomous systems (AS), which use the Border Gateway Protocol (BGP) to exchange routing information. 

An autonomous system is a set of Internet routable IP prefixes belonging to a network or a collection of networks that are all managed and supervised by a single entity or organisation. An AS utilises a common routing policy controlled by the entity and is identified by a globally unique 16 or 32-bit number. The AS number (ASN) is assigned by one of the five Regional Internet Registries (RIRs), just like IP address blocks.

The Border Gateway Protocol manages the routed peerings, prefix advertisement and routing of packets between different autonomous systems across the Internet. BGP uses the ASN to uniquely identify each system. In short, BGP is the routing protocol for AS paths across the Internet. The system is very dynamic and flexible by design. Connectivity and routing topologies are subject to change, which easily propagate globally within a few minutes. 

Fundamentally, BGP is based on mutual trust between networks. When a network operator configures the routers in their AS, they specify which IP prefixes to originate and announce to their peers. There is no authentication or authorisation embedded within BGP. In principle, an operator can define any ASN as the origin and announce any prefix, also one they are not the holder of. 

BGP Best Path Selection
-----------------------

BGP routing information includes the complete route to each destination. BGP uses the routing information to maintain a database of network reachability information, which it exchanges with other networks. For each prefix in the routing table, BGP continuously and dynamically makes decisions about the best path to reach a particular destination. After the best path is selected, the route is installed in the routing table.

Though there are many factors at play, two of them are the most important to keep in mind throughout the next sections: the preference for shortest path and most specific IP prefix.

Preference for Shortest Path
""""""""""""""""""""""""""""

Out of all the possible routes that a router has in its Routing Information Base (RIB), BGP will always prefer the shortest path to its destination, minimising the amount of hops. When two matching prefixes are announced from two different networks on the Internet, BGP will route traffic to the destination that is topologically closest. This is an important feature of BGP, but when configuration errors occur, it can also be the cause of reachability problems.

.. figure:: img/hijack-shorter-path.*
    :align: center
    :width: 100%
    :alt: When the same prefix is announced, the shortest path wins

    When the announcement of a prefix is an exact match, the shortest path wins

Preference for Most Specific Prefix
"""""""""""""""""""""""""""""""""""

Regardless any local preference, path length or any other attributes, when building the forwarding table, the router will always select most specific IP prefix available. This behaviour is important, but creates the possibility for almost any network to attract someone else's traffic by announcing an overlapping more specific.

.. figure:: img/hijack-more-specific.*
    :align: center
    :width: 100%
    :alt: A more specific prefix always wins

    Regardless of the path length, the announcement of a more specific prefix always wins
    
With this in mind, there are several problems that can arise as a result of this behaviour.

Routing Errors
--------------

Routing errors on the Internet can be classified as route leaks or route hijacks. `RFC 7908 <https://tools.ietf.org/html/rfc7908>`_ provides a working definition of a BGP route leak: 

   A route leak is the propagation of routing announcement(s) beyond
   their intended scope.  That is, an announcement from an Autonomous
   System (AS) of a learned BGP route to another AS is in violation of
   the intended policies of the receiver, the sender, and/or one of the
   ASes along the preceding AS path.  The intended scope is usually
   defined by a set of local redistribution/filtering policies
   distributed among the ASes involved.  Often, these intended policies
   are defined in terms of the pair-wise peering business relationship
   between autonomous systems.
   
A route hijack, also called prefix hijack, or IP hijack, is the unauthorised origination of a route. 

.. note:: Route leaks and hijacks can be accidental or malicious, but most often arise 
          from **accidental misconfigurations**. The result can be redirection of traffic
          through an unintended path. This may enable eavesdropping or traffic analysis
          and may, in some cases, result in a denial of service or black hole.

Routing incidents occur every day. While several decades ago incidents were often accidental, in recent years they have become more malicious in nature. Some notable events were the `AS 7007 incident <https://en.wikipedia.org/wiki/AS_7007_incident>`_ in 1997, Pakistan's attempt to block YouTube access within their country, which resulted in `taking down YouTube entirely <https://www.ripe.net/publications/news/industry-developments/youtube-hijacking-a-ripe-ncc-ris-case-study>`_ in 2008, and lastly, the `almost 1,300 addresses for Amazon Route 53 that got rerouted <https://arstechnica.com/information-technology/2018/04/suspicious-event-hijacks-amazon-traffic-for-2-hours-steals-cryptocurrency/>`_ for two hours in order to steal cryptocurrency, in 2018.

Mitigation of Routing Errors
----------------------------

One weakness of BGP is that routing errors cannot be easily be deduced from information within the protocol itself. For this reason, network operators have to carefully gauge what the intended routing policy of their peers is. As a result, it is imperative that networks employ filters to only accept legitimate traffic and drop everything else. 

There are several well known methods to achieve this. Certain backbone and private peers require a valid Letter of Agency (LOA) to be completed prior to allowing the announcement or re-announcement of IP address blocks. A more widely accepted method is the use of Internet Routing Registry (IRR) databases, where operators can publish their routing policy. Both methods allow other networks to set up filters accordingly.

The Internet Routing Registry
-----------------------------

The Internet Routing Registry (IRR) is a `distributed set of databases <http://www.irr.net/docs/list.html>`_ allowing network operators to describe and query for routing intent. The IRR is used as verification mechanism of route origination and is widely, though not universally, deployed to prevent accidental or intentional routing disturbances. 

The notation used in the IRR is the Routing Policy Specification Language (RPSL), which was originally defined in `RFC 2280 <https://tools.ietf.org/html/rfc2280>`_ in 1998. RPSL is a very expressive language, allowing for an extremely detailed description of routing policy. While IRR usage had created considerable early enthusiasm and has seen quite some traction, the Internet was rapidly growing at the time, which meant that the primary focus was on data availability rather than data trustworthiness.

As explained earlier, only the Regional Internet Registries have authoritative information on the legitimate holder of an Internet number resource. This means that the entries in their IRR databases are authenticated, but they are not in any of the other routing registries. Over time, this has created an extensive repository of obsolete data of uncertain validity, spread across dozens of routing registries around the world. 

Additionally, the RPSL language and supporting tools have proven to be too complex to consistently transpose policy into router configuration language. This resulted in most published RPSL data being neither sufficiently accurate and up to date for filtering purposes, nor sufficiently comprehensive or precise for being the golden master in router configuration.

This leaves the information about route origin as the most valuable attribute of the IRR. In so called *route* objects, operators can specify from which ASN they intend to announce a certain prefix. Based on these objects, other operators can define filters while keeping in mind that not all data is equally trustworthy.

In conclusion, the main weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. The result is that out of all the information on routing intent that is published, it is difficult to determine what is legitimate, authentic data and what isn’t. 

RPKI solves these problems, as you can be absolutely sure that an authoritative, cryptographically verifiable statement can be made by any legitimate IP resource holder in the world. In the next sections we will look at how this is achieved.