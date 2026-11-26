.. _doc_rpki_aspa:

.. index:: ASPA, Autonomous System Provider Authorisation

Autonomous System Provider Authorisation (ASPA)
===============================================

An ASPA lets the holder of an Autonomous System Number (ASN) publish a signed
list of the ASes it authorises as its providers. Networks can use these
declarations to check the AS path of a BGP announcement. This complements
route origin validation, which checks whether an AS is authorised to originate
a prefix. A route can have an authorised origin and still be leaked, for
example when a customer advertises a route learned from one transit provider
to another transit provider.

Publishing a provider list
--------------------------

Publish an ASPA through your hosted RPKI service or delegated certification
authority. List every AS that provides transit to your AS, including backup
providers and those that provide transit only at certain locations or for one
address family. The same list applies to IPv4 and IPv6. An ordinary peering
relationship by itself does not make the peer a provider.

For example, if AS64496 buys transit from AS64497 and uses AS64498 for backup
transit, AS64496 lists both providers in its ASPA. When adding a provider,
publish the updated list in time for other networks to receive it before they
see routes using the new relationship. An omitted provider can cause a
legitimate path to be classified as Invalid.

An AS that has no providers can declare this explicitly by listing AS0 as its
only provider. An AS that has published no ASPA has made no such declaration.
Publishing an ASPA makes it available to other networks regardless of whether
you use ASPA verification on your own routers.

From the repository to the router
---------------------------------

A relying party retrieves the signed object and validates it against the RPKI
certificate hierarchy. If several valid ASPAs exist for the same customer AS,
their provider lists are combined. AS0 remains in the combined list only when
all contributing lists contain AS0 alone.

A router can receive the resulting customer and provider ASNs from a relying
party over version 2 of the RPKI-to-Router (RTR) protocol. To use them in
routing policy, the routing software must also implement ASPA-based AS-path
verification. It checks the path against the available provider declarations,
taking into account the relationship with the neighbour that announced the
route. The signed objects attest to provider relationships. They do not prove
that an individual announcement travelled along the advertised path.

Interpreting the result
-----------------------

* **Valid** means the path satisfies the applicable ASPA verification checks.
* **Invalid** means the path fails those checks. A route leak or an incomplete
  provider declaration can produce this result.
* **Unknown** means the available information is insufficient to establish
  either result. A missing declaration can leave a relationship unknown while
  other evidence still establishes that the path is invalid.

Routing policy determines what happens to a route after verification. When
introducing ASPA checks, operators can inspect the results before changing
route acceptance. For an unexpected result, compare the advertised AS path,
the configured relationship with the neighbour and the provider data actually
available to the router.

Specifications
--------------

The IETF's `ASPA object profile
<https://datatracker.ietf.org/doc/draft-ietf-sidrops-aspa-profile/>`_
describes publication, object validation and the treatment of multiple objects.
The `AS-path verification draft
<https://datatracker.ietf.org/doc/draft-ietf-sidrops-aspa-verification/>`_
defines how routers evaluate paths, including paths received through IXP route
servers. The `RTR specification
<https://datatracker.ietf.org/doc/draft-ietf-sidrops-8210bis/>`_ describes
how relying parties deliver validated data to routers.
