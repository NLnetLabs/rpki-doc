.. _doc_about_terminology:

Introduction
============

Throughout this documentation you will find references to standards, specific
terminology, and various implementation choices. This page is meant to introduce
you to some of the specifics of RPKI. 

ROAs, VRPs and ROV
------------------

It's quite common to use the term ROA and VRP interchangably, but they are quite
different.

Route Origin Attestations (ROAs) are cryptographically signed objects that
contain a statement authorising a *single* Autonomous System Number (ASN) to
originate one or more IP prefixes, along with their maximum prefix length. A ROA
can only be created by the legitimate holder of the IP prefixes contained witin
it.

RPKI Relying Party software performs cryptographic verification on all published
ROAs. If everything checks out, the software will produce one or more validated
ROA payloads (VRPs) for each ROA, depending on how many IP prefixes are
contained with in it. Each VRP is a tuple of an ASN, a single prefix and its
maximum prefix length. If verification fails, the ROA is discarded and it'll be
like no statement was ever made. 

The collection of all VRPs can be compared to the BGP route announcements seen
by your routers. This process is called Route Origin Validation (ROV).

Verification, Validation and Validity
-------------------------------------

The terms "Valid" and "Invalid" are often used in different contexts, which can
be confusing. 

As explained, ROAs and all related crytographic objects are verified by Relying
Party Software. If they pass verification, one or more VRPs are emitted and each
is compared to a BGP route. If the route origin is authorised by the VRP, it is
considered "RPKI Valid", if it isn't it is "RPKI Invalid". If nothing can be
said about the validity of the route, it is considered "RPKI NotFound".

Only a ROA that has passed cryptographic verification – i.e. a *Valid ROA* – can
make a BGP route "RPKI Valid" or "RPKI Invalid". 
