Hosted versus Delegated RPKI
----------------------------

The five RIRs are responsible for allocating IP addresses and Autonomous System Numbers in each of their regions. In most cases they allocate resources directly to their members, called Local Internet Registries (LIRs). In turn, LIRs assign resources to ISPs or end user organisations. There is an exception in the APNIC and LACNIC region, where in some countries allocations are handled through National Internet Registry (NIR) organisations to meet particular geographical needs. For example, JPNIC in Japan and NIC.br in Brazil provide these services on a national level.

The RPKI certificate structure matches the allocation model. This means all RIRs run a Root Certificate Authority which has a self-signed root certificate containing all of the resources that they each manage. They issue child certificates to NIRs or LIRs who can, in turn, issue a certificate to each of their customers. 

.. figure:: RPKI-CA-Structure.svg
    :align: center
    :width: 100%
    :alt: The RPKI Certificate Authority Structure

    An overview of the RPKI Certificate Authority Structure

To lower the entry barrier into the technology, each RIR offers a Hosted RPKI system. This allows members to log into a web-based customer portal and instruct the RPKI system to generate a certificate for them, which resides on the systems hosted by the RIR. While this offers some convenience for basic management, the LIR is not truly the holder of their own certificate. Operators who prefer more control, security and have better integration with their own systems can run their own child CA. This is model is usually referred to as Delegated RPKI.

Using either the Hosted or Delegated RPKI, operators can create cryptographically validatable statements about their routing intent, e.g. which ASNs are authorised to originate their IP prefixes. These statements are called Route Origin Authorisations (ROAs).  Within a ROA, the operator can also specify what the maximum length of the prefix is that the AS is authorised to advertise. This is a way to enforce aggregation and prevent hijacking through the announcement of a more specific prefix.

All certificates and ROAs are published in a distributed set of repositories. When using the Hosted RPKI system, this is a repository operated by the RIR. When an operator uses Delegated RPKI, they can either publish to a Publication Server that they host themselves, or they can ask a third party to do this on their behalf. 