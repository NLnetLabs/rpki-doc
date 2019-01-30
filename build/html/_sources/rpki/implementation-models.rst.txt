.. _doc_implementation_models:

Implementation Models
=====================

RPKI is designed to allow every resource holder generate and publish cryptographic material on their own systems. This is commonly referred to as delegated RPKI. To offer a turn-key solution, each RIR also offers a hosted RPKI system in their member portals. Both models have their own advantages, based on the specific requirements of the organisation using the system. 

Hosted RPKI
-----------

When the five RIRs committed to offer RPKI services around 2008, it was clear that there would be an early adopters phase for a considerable amount of time. Given the past experiences with IPv6 and DNSSEC uptake, the RIRs decided to offer a hosted RPKI solution to lower the entry barrier into the technology. This way, organisations could easily get operational experience with the technology, without having to manage a certificate authority themselves.

Hosted RPKI offers a fair balance between ease-of-use, maintenance and flexibility. It allows users to log into their RIR member portal and request a resource certificate, which is hosted on the servers of the RIR. All cryptographic operations, such as key roll overs, are automated. There is nothing that the user has to manage.

.. figure:: img/ripe-ncc-hosted-rpki.png
    :align: center
    :width: 100%
    :alt: Example of the Hosted RPKI interface by the RIPE NCC

    Example of the Hosted RPKI interface by the RIPE NCC

While this offers some convenience for basic management, the LIR is not truly the holder of their own certificate. 

All certificates and ROAs are published in a distributed set of repositories. When using the Hosted RPKI system, this is a repository operated by the RIR. 

Delegated RPKI
--------------

Operators who prefer more control and have better integration with their own systems can run their own child CA. This is model is usually referred to as Delegated RPKI.

When an operator uses Delegated RPKI, they can either publish to a Publication Server that they host themselves, or they can ask a third party to do this on their behalf. 