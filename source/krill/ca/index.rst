Certificate Authority
=====================

This implementation allows operators to run their own Certificate Authority (CA) as a child of a Regional Internet Registry or a different parent, such as a National Internet Registry (NIR) or Enterprise. The CA will allow operators to generate their own cryptographic material, including all certificates and ROAs. 

The software will support running the CA both upwards and downwards. Upwards means that operators can have multiple parents, such as ARIN, RIPE NCC, etc., simultaneously and transparently. Downwards means that the CA can issue to child organisations or customers who, in turn, run their own CA.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   configuration
   api