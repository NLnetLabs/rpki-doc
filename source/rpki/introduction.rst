.. _doc_rpki_introduction:

Introduction
============

Resource Public Key Infrastructure (RPKI) revolves around the right to use
Internet number resources, such as IP addresses and autonomous system (AS)
numbers.

In this PKI, the legitimate holder of a block of IP addresses or AS numbers can
obtain a resource certificate. Using the certificate, they can make
authoritative, signed statements about the resources listed on it. To understand
the structure of RPKI and its usage, we must first look at how Internet number
resources are allocated globally.

Internet Number Resource Allocation
-----------------------------------

Before being formalised within an organisation, the allocation of Internet
number resources, such as IP addresses and AS numbers, had been the
responsibility of `Jon Postel <https://en.wikipedia.org/wiki/Jon_Postel>`_. At
the time, he worked at the Information Sciences Institute (ISI) of the
University of Southern California (USC). He performed the role of `Internet
Assigned Numbers Authority
<https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority>`_ (IANA),
which is presently a function of the `Internet Corporation for Assigned Names
and Numbers <https://en.wikipedia.org/wiki/ICANN>`_ (ICANN).

.. figure:: img/jon-postel.jpg
    :align: center
    :width: 100%
    :alt: Jon Postel in 1994, with map of Internet top-level domains

    Jon Postel in 1994, with a map of Internet top-level domains

Initially, the IANA function was performed globally, but as the work volume grew
due to the expansion of the Internet, `Regional Internet Registries
<https://en.wikipedia.org/wiki/Regional_Internet_registry>`_ (RIRs) were
established over the years to take on this responsibility on a regional level.
Until the available pool of IPv4 depleted in 2011, this meant that periodically,
a large block of IPv4 address space was allocated from IANA to one of the RIRs.
In turn, the RIRs would allocate smaller blocks to their member organisations,
and so on. IPv6 address blocks and AS numbers are allocated in the same way.

Today, there are five RIRs responsible for the allocation and registration of
Internet number resources within a particular region of the world:

- The `African Network Information Center <https://www.afrinic.net/>`_ (AFRINIC) serves Africa
- The `American Registry for Internet Numbers <https://www.arin.net/>`_ (ARIN) serves Antarctica, Canada, parts of the Caribbean, and the United States
- The `Asia-Pacific Network Information Centre <https://www.apnic.net/>`_ (APNIC) serves East Asia, Oceania, South Asia, and Southeast Asia
- The `Latin America and Caribbean Network Information Centre <https://www.lacnic.net/>`_ (LACNIC) serves most of the Caribbean and all of Latin America
- The `Réseaux IP Européens Network Coordination Centre <https://www.ripe.net/>`_ (RIPE NCC) serves Europe, the Middle East, Russia, and parts of Central Asia

.. figure:: img/rir-world-map.*
    :align: center
    :width: 100%
    :alt: Service regions of the Regional Internet Registries

    The service regions of the five Regional Internet Registries

In the APNIC and LACNIC regions, Internet number resources are in some cases
allocated to National Internet Registries (NIRs), such as NIC.br in Brazil and
JPNIC in Japan. NIRs allocate address space to its members or constituents,
which are generally organised at a national level. In the rest of world, the
RIRs allocate directly to their member organisations, typically referred to as
Local Internet Registries (LIRs). Most LIRs are Internet service providers,
enterprises, or academic institutions. LIRs either use the allocated IP address
blocks themselves, or assign them to End User organisations.

.. figure:: img/ip-allocation-structure.*
    :align: center
    :width: 100%
    :alt: Internet number resource allocation hierarchy

    Internet number resource allocation hierarchy

Mapping the Resource Allocation Hierarchy into the RPKI
-------------------------------------------------------

As illustrated, the IANA has the authoritative registration of `IPv4
<https://www.iana.org/assignments/ipv4-address-space/ipv4-address-space.xhtml>`_,
`IPv6
<https://www.iana.org/assignments/ipv6-unicast-address-assignments/ipv6-unicast-address-assignments.xhtml>`_
and `AS number <https://www.iana.org/assignments/as-numbers/as-numbers.xhtml>`_
resources that are allocated to the five RIRs. Each RIR `registers
<https://www.nro.net/about/rirs/statistics/>`_ authoritative information on the
allocations to NIRs and LIRs, and lastly, LIRs record to which End User
organisation they assigned resources.

In RPKI, resource certificates attest to the allocation by the issuer of IP
addresses or AS numbers to the subject. As a result, the certificate hierarchy
in RPKI follows the same structure as the Internet number resource allocation
hierarchy, with the exception of the IANA level. Instead, the five RIRs each run
a root CA with a trust anchor from which a chain of trust for the resources they
each manage is derived.

.. figure:: img/rpki-trust-chain.*
    :align: center
    :width: 100%
    :alt: The chain of trust in RPKI starting at the five RIRs

    The chain of trust in RPKI, starting at the five RIRs

The IANA does not operate a single root certificate authority (CA). While this
was originally a `recommendation
<https://www.iab.org/documents/correspondence-reports-documents/docs2010/iab-statement-on-the-rpki/>`_
from the Internet Architecture Board (IAB) to eliminate the possibility of
resource conflicts in the system, they `reconsidered
<https://www.iab.org/documents/correspondence-reports-documents/2018-2/iab-statement-on-the-rpki/>`_
after operational experience in deployment had caused the RIRs to conclude that
the RPKI system would be less brittle using multiple `overlapping trust anchors
<https://www.nro.net/regional-internet-registries-are-preparing-to-deploy-all-resources-rpki-service/>`_.

X.509 PKI Considerations
------------------------

The digital certificates used in RPKI are based on X.509, standardised in `RFC
5280 <https://tools.ietf.org/html/rfc5280>`_, along with extensions for IP
addresses and AS identifiers described in `RFC 3779
<https://tools.ietf.org/html/rfc3779>`_. Because RPKI is used in the routing
security context, a common misconception is that this is the *Routing* PKI.
However, certificates in this PKI are called *resource* certificates and conform
to the certificate profile described in `RFC 6487
<https://tools.ietf.org/html/rfc6487>`_.

.. note:: X.509 certificates are typically used for authenticating either an
          individual or, for example, a website. **In RPKI, certificates
          do not include identity information**, as their only purpose is to
          transfer the right to use Internet number resources.

In addition to RPKI not having any identity information, there is another
important difference with commonly used X.509 PKIs, such as SSL/TLS. Instead of
having to rely on a  vast number of root certificate authorities which come
pre-installed in a browser or an operating system, RPKI relies on just five
trust anchors, run by the RIRs. These are well established, openly governed,
not-for-profit organisations. Each organisation that wishes to get an RPKI
resource certificate already has a contractual relationship with one or more of
the RIRs.

In conclusion, RPKI provides a mechanism to make strong, testable attestations
about Internet number resources. In the next sections, we will look at how this
can be used to make Internet routing more secure.
