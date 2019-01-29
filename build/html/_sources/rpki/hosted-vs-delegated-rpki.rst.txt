.. _doc_rpki_hosted_delegated:

Hosted versus Delegated RPKI
============================

To lower the entry barrier into the technology, each RIR offers a Hosted RPKI system. This allows members to log into a web-based customer portal and instruct the RPKI system to generate a certificate for them, which resides on the systems hosted by the RIR. While this offers some convenience for basic management, the LIR is not truly the holder of their own certificate. Operators who prefer more control, security and have better integration with their own systems can run their own child CA. This is model is usually referred to as Delegated RPKI.

All certificates and ROAs are published in a distributed set of repositories. When using the Hosted RPKI system, this is a repository operated by the RIR. When an operator uses Delegated RPKI, they can either publish to a Publication Server that they host themselves, or they can ask a third party to do this on their behalf. 