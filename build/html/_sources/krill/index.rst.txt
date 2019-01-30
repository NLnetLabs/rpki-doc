.. WARNING::  This part of the project is currently being built. 
              Documentation will likely change significantly as the software evolves.

Krill
=====

Krill is a free, open source Resource Public Key Infrastructure (RPKI) daemon, 
featuring a Certificate Authority and Publication Server, written in Rust. 

This implementation will allow operators to run their own Certificate Authority (CA) as a child of a Regional Internet Registry or a different parent, such as a National Internet Registry (NIR) or Enterprise. The CA will allow operators to generate and publish their own cryptographic material, including all certificates and ROAs.

The software will support running the CA both upwards and downwards. Upwards means that operators can have multiple parents, such as ARIN, RIPE NCC, etc., simultaneously and transparently. Downwards means that the CA can issue to child organisations or customers who, in turn, run their own CA.

The CA is intended for:

- Operators who require easier RPKI management that is integrated with their own systems in a better way, instead of relying on the web-based user interface that the RIRs offer with the hosted systems
- Operators who are security conscious and require that they are the only ones in possession of the private key of a system they use
- Operators who want to be operationally independent from the parent RIR, such as NIRs or Enterprises

The Publication Server in Krill can also be run as an independent component. This can be used by organisations who want to offer publication of RPKI data as a service. This way, it will allow operators to do the publication of their certificates and ROAs themselves, or let a third party such as a Content Delivery Network do it.

If you want to know more about the project planning, please have a look at the
high level `roadmap <https://nlnetlabs.nl/projects/rpki/project-plan/>`_ on
our website, or get at a more detailed overview of the `milestones <https://github.com/NLnetLabs/krill/milestones?direction=asc&sort=due_date&state=open>`_
on GitHub. If you have any questions, comments or ideas, you are welcome  to discuss them
on our `RPKI mailing list <https://nlnetlabs.nl/mailman/listinfo/rpki>`_, or feel 
free to `create an issue <https://github.com/NLnetLabs/krill/issues>`_ on GitHub.

.. toctree::
   :maxdepth: 2
   :name: toc-krill

   installation
   ca/index
   pub/index
   
.. history
.. authors
.. license