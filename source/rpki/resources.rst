.. _doc_rpki_resources:
.. index:: ! Resources


Resources
=========

This page provides an overview of projects that support RPKI. It includes,
statistics, measurements projects and presentations about operational
experiences. Finally, there is an overview of all work in the Internet
Engineering Task Force relevant to RPKI.

The :ref:`doc_tools` page an overview of all available tools for using RPKI.

.. index:: Books

Books
-----

:download:`BGP RPKI: Instructions for use <https://labs.ripe.net/Members/flavio_luciani_1/bgp-rpki-instructions-for-use>`, by Flavio Luciani & Tiziano Tofoni (PDF)

:download:`Juniper Day One: Deploying BGP Routing Security <https://www.juniper.net/documentation/en_US/day-one-books/DO_BGP_SecureRouting2.0.pdf>`, by Melchior Aelmans & Niels Raijer (PDF)

.. index:: Statistics

Insights and Statistics
-----------------------

There are several initiatives that measure the adoption and data quality of RPKI:

- `Global country stats, with AS and IP prefix analysis <https://www.nlnetlabs.nl/projects/rpki/rpki-analytics/>`_, by NLnet Labs
- `Cirrus Certificate Transparency Log <https://ct.cloudflare.com/logs/cirrus>`_, by Cloudflare
- `Global certificate and ROA statistics <http://certification-stats.ripe.net>`_, by RIPE NCC
- `RPKI Deployment Monitor <https://rpki-monitor.antd.nist.gov>`_, by NIST
- `The RPKI Observatory <https://nusenu.github.io/RPKI-Observatory/>`_, by nusenu
- `RPKI connection test <http://sg-pub.ripe.net/jasper/rpki-web-test/>`_, by RIPE Labs
- `The rpki-client console <http://console.rpki-client.org>`_, by the OpenBSD project
- `JDR: explore, inspect and troubleshoot anything RPKI <https://jdr.nlnetlabs.nl/>`_, by NLnet Labs
- `RPKIviews: download historic raw RPKI data <http://www.rpkiviews.org/>`_, by Job Snijders
- `ROA Use World Map and Country Data <https://stats.labs.apnic.net/roas>`_, by APNIC Labs
- `RPKI Portal <https://rpki.cloudflare.com>`_, by Cloudflare

.. index:: Operational experiences

Operational Experiences
-----------------------

`RPKI Deployment Considerations for ISPs https://docs.google.com/document/d/1fGsuDpLSn0ZN3-Pa-4aAciGH-Qc0K5AHZ1GyFRAHow4/edit?usp=sharing`_
   Document by Rich Compton - Charter Communications, with contributions from the operator community

`Using RPKI with IXP Manager <https://docs.ixpmanager.org/features/rpki/>`_
   Documentation to set up Routinator, OctoRPKI and the RIPE NCC Validator with BIRD 2.x

`Use Routinator with Cisco IOS-XR <https://beufa.net/blog/rpki-use-routinator-rtr-cache-validator-cisco-ios-xr/>`_
   Blog post by Fabien Vincent

`Wikimedia RPKI Validation Implementation <https://phabricator.wikimedia.org/T220669>`_
   Documentation by Arzhel Younsi describing RPKI validator and router configuration

`Dropping RPKI invalid routes in a service provider network <https://www.youtube.com/watch?v=DkUZvlj1wCk>`_
   Lightning talk by Nimrod Levy - AT&T, NANOG 75, February 2019

`RPKI and BGP: our path to securing Internet Routing <https://blog.cloudflare.com/rpki-details/>`_
   Blog post by Jérôme Fleury & Louis Poinsignon - Cloudflare, September 2018

`RPKI For Managers <https://www.youtube.com/watch?v=vrzl__yGqLE>`_
   Presentation by Niels Raijer - Fusix Networks, NLNOG Day 2018, September 2018

`RPKI at IXP Route Servers <https://ripe78.ripe.net/archives/video/53/>`_
   Presentation by Nick Hilliard - INEX, RIPE 78, May 2019

`Lessons learned: NTT's RPKI deployment <https://www.youtube.com/watch?v=1ak4hF2j84o>`_
   Presentation by Job Snijders - NANOG 79, June 2020

.. index:: Examples of BGP hijacks

Examples of BGP Hijacks
-----------------------

`How Verizon and a BGP Optimizer Knocked Large Parts of the Internet Offline Today <https://blog.cloudflare.com/how-verizon-and-a-bgp-optimizer-knocked-large-parts-of-the-internet-offline-today/>`_
   Cloudflare Blog, 24 June 2019

`BGP / DNS Hijacks Target Payment Systems <https://blogs.oracle.com/internetintelligence/bgp-dns-hijacks-target-payment-systems>`_
   Oracle Internet Intelligence, 3 August 2018

`Shutting down the BGP Hijack Factory <https://dyn.com/blog/shutting-down-the-bgp-hijack-factory/>`_
   Oracle Dyn, 10 July 2018

`Suspicious event hijacks Amazon traffic for 2 hours, steals cryptocurrency <https://arstechnica.com/information-technology/2018/04/suspicious-event-hijacks-amazon-traffic-for-2-hours-steals-cryptocurrency/>`_
   Ars Technica, 24 April 2018

`Popular Destinations rerouted to Russia <https://bgpmon.net/popular-destinations-rerouted-to-russia/>`_
   BGPmon, 12 December 2017

`Insecure routing redirects YouTube to Pakistan <https://arstechnica.com/uncategorized/2008/02/insecure-routing-redirects-youtube-to-pakistan/>`_
   Ars Technica, 25 February 2008

.. index:: IETF Documents
  see: RFCs about RPKI; IETF Documents

IETF Documents
--------------

Most of the original work on RPKI standardisation for both origin and path
validation was done in the `Secure Inter-Domain Routing (sidr)
<https://tools.ietf.org/wg/sidr/>`_ working group. After the work was completed,
the working group was concluded.

Since then, the `SIDR Operations (sidrops)
<https://tools.ietf.org/wg/sidrops/>`_ working group was formed. This working
group develops guidelines for the operation of SIDR-aware networks, and provides
operational guidance on how to deploy and operate SIDR technologies in existing
and new networks.

All relevant drafts and standards can be found in the archives of these two
working groups, with a few exceptions, such as `draft-ietf-grow-rpki-as-cones
<https://tools.ietf.org/html/draft-ietf-grow-rpki-as-cones>`_.
