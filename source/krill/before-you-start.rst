.. _doc_krill_before_you_start:

Before You Start
================

Before you begin with installing Krill, there are some basic concepts you should
understand and some decisions you need to make.

RPKI is a very modular system and so is Krill. Which parts you need and how you
fit them together depends on your situation. The two fundamental pieces at play.
First there is the Certificate Authority (CA), which takes care of all the
cryptographic operations. Secondly, there is the publication server, which makes
your certificate and ROAs available to the world.

In almost all cases you will need to run the CA that Krill provides under a
parent, usually your Regional Internet Registry (RIR) or National Internet
Registry (NIR). The communication between the parent and the child CA is
initiated through the exchange of two XML files which you need to handle
manually: a child request XML and a parent response XML. This involves
generating a file, providing it to your parent in some way (usually through a
web portal) and giving the response file back to your CA. After this initial
exchange has been completed, all subsequent requests and responses are handled
by the parent and child CA themselves. This includes the entitlement request and
response, the certificate request and response, as well as the revoke request
and response.

Whether you also run the publication server depends if a if you can, or want to
use one offered by a third party. For the general wellbeing of the RPKI system,
we would always recommend to publish with your parent CA, if available. Setting
this up is done in the same way as with the CA: exchanging a publisher request
XML and a repository response XML.

Publishing With Your Parent
---------------------------

If you can use a publication server provided by your parent, the installation
and configuration of Krill becomes extremely easy. After the installation has
completed, you perform the XML exchange twice and you are done.

.. figure:: img/parent-child-rir-nir-repo.*
    :align: center
    :width: 100%
    :alt: A repository hosted by the parent CA

    A repository hosted by the parent CA, in this case the RIR or NIR.

Krill is designed to run continuously, but there is no strict uptime
requirement for the CA. You can bring Krill down to perform upgrades and
backups, as long as you bring it back up within 16 hours to ensure your
cryptographic objects are resigned.

.. Note:: This scenario also applies if you use an RPKI publication server
          offered by a third party, such as a cloud provider.

At this time, only Asia Pacific RIR APNIC and Brazilian NIR NIC.br offer a
publication server for their members. Other RIRs have this functionality on
their roadmap. This means that in most cases, you will have to publish
yourself.

Publishing Yourself
-------------------

When you publish your certificate and ROAs yourself, you are faced with
running a public service with all related responsibilities, such as uptime and
DDoS protection.

Krill can be configured with two types of publication server: embedded and
stand-alone. Using the embedded publication server is simple, and doesn't
require a publisher request and repository response exchange. However, it is
practically impossible to change its configuration after it has been
initialised.

For production environments where you may want change strategies over time we
recommend running a separate Krill instance running as a repository only. This
also allows you to host a publication server for others, such as children of
your own.

.. figure:: img/parent-child-repo.*
    :align: center
    :width: 100%
    :alt: Running your own publication server

    Running a publication server for yourself and your children

In this scenario you install Krill on a separate, highly available machine and
simply don't set up any CA. In addition, you will need to run Rsyncd and a web
server of your choice to publish your certificate and ROAs.
