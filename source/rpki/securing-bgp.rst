.. _doc_rpki_securing_bgp:
.. index:: Securing BGP

Securing BGP
============

Now that we've looked at how the RPKI structure is built and understand the
basics of Internet routing, we can look at how RPKI can be used to make BGP more
secure.

Networks use RPKI data to check both the origin of an announcement and
provider relationships along its AS path. These checks address different
routing problems and can be used together.

.. index:: Route Origin Validation

.. _rov:

Route Origin Validation
-----------------------

With route origin validation (ROV), the RPKI system tries to closely mimic what
*route* objects in the IRR intend to do, but then in a more trustworthy manner.
It also adds a couple of useful features.

Origin validation is currently the only functionality that is operationally
used. The five RIRs provide functionality for it, there is open source software
available for creation, publication and use of data, and all major router vendors
have implemented ROV in their platforms. Various router software implementations
offer support for it, as well.

.. index:: Route Origin Authorisations
  see: ROAs; Route Origin Authorisations

Route Origin Authorisations
"""""""""""""""""""""""""""

Using the RPKI system, the legitimate holder of a block of IP addresses can use
their resource certificate to make an authoritative, signed statement about
which autonomous system is authorised to originate their prefix in BGP. These
statements are called Route Origin Authorisations (ROAs).

.. figure:: img/rpki-roa-creation.*
    :align: center
    :width: 100%
    :alt: RPKI ROA Creation

    Each CA can issue Route Origin Authorisations

The creation of a ROA is solely tied to the IP address space that is listed on
the certificate and not to the AS numbers. This means the holder of the
certificate can authorise any AS to originate their prefix, not just their own
autonomous systems.

.. index:: Maximum Prefix Length
  see: MaxLength; Maximum Prefix Length

Maximum Prefix Length
~~~~~~~~~~~~~~~~~~~~~

In addition to the origin AS and the prefix, the ROA contains a maximum length
(maxLength) value. This is an attribute that a *route* object in RPSL doesn't
have. Described in :RFC:`6482`, the maxLength specifies the maximum
length of the IP address prefix that the AS is authorised to advertise. This
gives the holder of the prefix control over the level of deaggregation an AS is
allowed to do.

For example, if a ROA authorises a certain AS to originate 192.0.1.0/24 and the
maxLength is set to /25, the AS can originate a single /24 or two adjacent /25
blocks. Any more specific announcement is unauthorised by the ROA. Using this
example, the shorthand notation for prefix and maxLength you will often
encounter is ``192.0.1.0/24-25``.

.. WARNING:: According to :RFC:`7115`, operators should be
             conservative in use of maxLength in ROAs. For example, if a prefix
             will have only a few sub-prefixes announced, multiple ROAs for the
             specific announcements should be used as opposed to one ROA with a
             long maxLength.

             **Liberal usage of maxLength opens up the network to a forged origin
             attack. ROAs should be as precise as possible, meaning they should
             match prefixes as announced in BGP.**

In a forged origin attack, a malicious actor spoofs the AS number of another
network. With a minimal ROA length, the attack does not work for sub-prefixes
that are not covered by overly long maxLength. For example, if, instead of
10.0.0.0/16-24, one issues 10.0.0.0/16 and 10.0.42.0/24, a forged origin attack
cannot succeed against 10.0.66.0/24. They must attack the whole /16, which is
more likely to be noticed because of its size.

.. index:: RPKI validity
  see: Valid status; RPKI validity
  see: Invalid status; RPKI validity
  see: NotFound status; RPKI validity

Route Announcement Validity
"""""""""""""""""""""""""""

When a network operator creates a ROA for a certain combination of origin AS and
prefix, this will have an effect on the RPKI validity of one or more route
announcements. Once a ROA is validated, the resulting object contains an IP
prefix, a maximum length, and an origin AS number. This object is referred to as
validated ROA payload (VRP).

When comparing VRPs to route announcements seen in BGP, :RFC:`6811`
describes their possible statuses, which are:

Valid
   The route announcement is covered by at least one VRP. The term *covered* means that
   the prefix in the route announcement is equal, or more specific than the prefix in the
   VRP.

Invalid
   The prefix is announced from an unauthorised AS, or the announcement is more
   specific than is allowed by the maxLength set in a VRP that matches the
   prefix and AS.

NotFound
   The prefix in this announcement is not, or only partially covered by a VRP.

Anyone can download and validate the published certificates and ROAs and make
routing decisions based on these three outcomes. In the
:ref:`doc_rpki_relying_party` section, we'll cover how this works in practice.

.. index:: Path validation
  see: ASPA; Path validation
  see: BGPSec; Path validation

Path Validation
---------------

Route origin validation checks the origin AS of a prefix. An announcement can
pass this check even if an intermediate AS has leaked the route.

:ref:`doc_rpki_aspa` describes how networks use signed provider declarations
to check relationships along the AS path. BGPsec, specified in :RFC:`8205`,
uses signatures added by participating routers as they propagate an
announcement.
