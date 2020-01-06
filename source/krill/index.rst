Krill
=====

Krill is a free, open source Resource Public Key Infrastructure (RPKI) daemon,
featuring a Certification Authority (CA) and Publication Server, written by NLnet Labs
in the Rust programming language.

In the context of Krill we refer to a CA as unit that represents an organisational unit,
e.g. your company. This CA will typically have a single parent Certification Authority, like
the RIR/NIR that you have registered IP addresses and/or AS numbers with. However, you
may have multiple parents. It's also possible to delegate resources down children of your
own, e.g. business units, departments, members or clients.

Resources that you receive from each of your parents will each go on separate X509
certificates, and in fact you might even get resources from a single parent assigned to
you on different certificates. These certificates are often referred to as "CA certificates",
which can be somewhat confusing with regards to the term CA. A "CA certificate" is simply
a certificate that is allowed to sign delegated certificates in the RPKI. And an
'organisational' CA, as described above, will typically have one or many CA certificates.

So, here we always talk about 'organisational' CAs when we talk about CAs. In fact
the main reason of being for Krill is that it let's you think about your organisation
at this higher level, while Krill will deal with the management of lower level CA
certificates, and all the other moving parts that are used in the RPKI.

Krill is intended for:

- Operators who require easier RPKI management that is integrated with their own systems in a better way, instead of relying on the web-based user interface that the RIRs offer with the hosted systems
- Operators who are security conscious and require that they are the only ones in possession of the private key of a system they use
- Operators who want to be operationally independent from the parent RIR, such as NIRs or Enterprises

Krill currently features an `API <http://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/NLnetLabs/krill/v0.4.1/doc/openapi.yaml>`_ and a CLI. A UI, based on the API, is planned for
the near future, and will probably be released as a separate project.

If you want to know more about the project planning, please have a look at the
high level `roadmap <https://lists.nlnetlabs.nl/projects/rpki/project-plan/>`_ on
our website, or get at a more detailed overview of the `releases <https://github.com/NLnetLabs/krill/projects?query=is%3Aopen+sort%3Aname-asc/>`_
on GitHub.

If you have any questions, comments or ideas, you are welcome  to discuss them
on our `RPKI mailing list <https://nlnetlabs.nl/mailman/listinfo/rpki>`_, or feel
free to `create an issue <https://github.com/NLnetLabs/krill/issues>`_ on GitHub.

.. toctree::
   :maxdepth: 2
   :name: toc-krill

   installation
   running
   running-docker
   get-started
   using-cli
   using-api
   manage-cas
   remote-publishing
.. history
.. authors
.. license
