.. _doc_rpki_resources:

Resources
=========

This page provides an overview of projects that support RPKI. It includes, statistics, measurements projects and presentations about operational experiences. Finally, there is an overview of all work in the Internet Engineering Task Force relevant to RPKI.

The :ref:`doc_tools` page an overview of all available tools for using RPKI.

Insights and Statistics
-----------------------

There are several initiatives that measure the adoption and data quality of RPKI:

- `Global country stats, with AS and IP prefix analysis <https://www.nlnetlabs.nl/projects/rpki/rpki-analytics/>`_, by NLnet Labs
- `Cirrus Certificate Transparency Log <https://ct.cloudflare.com/logs/cirrus>`_, by Cloudflare
- `Global certificate and ROA statistics <http://certification-stats.ripe.net>`_, by RIPE NCC
- `RPKI Deployment Monitor <https://rpki-monitor.antd.nist.gov>`_, by NIST
- `The RPKI Observatory <https://nusenu.github.io/RPKI-Observatory/>`_, by nusenu
- `RPKI connection test <http://sg-pub.ripe.net/jasper/rpki-web-test/>`_, by RIPE Labs

Operational Experiences
-----------------------

`Dropping RPKI invalid routes in a service provider network <https://www.youtube.com/watch?v=DkUZvlj1wCk>`_
   Lightning talk by Nimrod Levy - AT&T, NANOG 75, February 2019
   
`RPKI and BGP: our path to securing Internet Routing <https://blog.cloudflare.com/rpki-details/>`_
   Blog post by Jérôme Fleury & Louis Poinsignon - Cloudflare, September 2018
   
`RPKI For Managers <https://www.youtube.com/watch?v=vrzl__yGqLE>`_
   Presentation by Niels Raijer - Fusix Networks, NLNOG Day 2018, September 2018

IETF Documents
--------------

Most of the original work on RPKI standardisation for both origin and path validation was done in the `Secure Inter-Domain Routing (sidr) <https://tools.ietf.org/wg/sidr/>`_ working group. After the work was completed, the working group was concluded.

Since then, the `SIDR Operations (sidrops) <https://tools.ietf.org/wg/sidrops/>`_ working group was formed. This working group develops guidelines for the operation of SIDR-aware networks, and provides operational guidance on how to deploy and operate SIDR technologies in existing and new networks.

All relevant drafts and standards can be found in the archives of these two working groups, with a few exceptions, such as `draft-azimov-sidrops-aspa-profile <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-profile>`_, `draft-azimov-sidrops-aspa-verification <https://tools.ietf.org/html/draft-azimov-sidrops-aspa-verification>`_ and `draft-ietf-grow-rpki-as-cones <https://tools.ietf.org/html/draft-ietf-grow-rpki-as-cones>`_.
