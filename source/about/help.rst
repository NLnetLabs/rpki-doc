RPKI Quick Help
===============

If you're reading this page, chances are you find yourself in a situation where you've been told by someone that your RPKI ROAs make your routes invalid and you don't know what that means.  The aim of the content on this page is to point you in the right direction and provide further resources that can be of assistance.  This page is not meant for experts, and many technicalities will be glossed over in order to be able to provide easy to understand answers for all knowledge levels.

What is RPKI or ROA?
--------------------
RPKI stands for Resource Public Key Infrastructure, ROA stands for Route Origin Authorisation.

What do they do?
----------------
They provide a method for the originator of a route to assert they are the correct originator and that other originators are not valid.

How does it work?
-----------------
The "root" assigner of all IP space (v4+v6) is IANA.  They have delegated this space to one of the RIRs (ARIN, RIPE NCC, APNIC, LACNIC, and AFRINIC).  In turn, those RIRs assign the space to other entities. Each RIR has a portal where the owner of the space can assert the origination ASN, which then generates a ROA for that particular combination of route and origination ASN.  This ROA is then published out by the RIR so that anyone can view them.

What is in a ROA?
-----------------
A ROA is a signed statement that consists of a prefix, a maximum prefix length, and originating ASN.

What happens next?
------------------
Any operator is free to get that list of ROAs from the RIRs and use that to tell their routers to take action based on the ROA.  A particular announcement will generally have one of three states:

NotFound (a.k.a. Unknown)
   This is the default state if no ROA has been made for the announcement.  It is expected that all operators will allow these routes to be installed in their routers.

Valid
   This is the state if the ROA and route announcement matches.  It is expected that all operators will allow these routes to be installed in their routers.  It is possible they may up-preference these routes.

Invalid
   This is the state if the ROA and route announcement are different.  They either differ in originating ASN or is more specific than is allowed by the maximum prefix length that is set in the ROA.  If an operator is using RPKI in a strict fashion, odds are good that this announcement will not be installed into their routers.

What can I do about my route having an Invalid state?
-----------------------------------------------------
The only entity that can make any changes to the ROA is the RIR-listed owner of the IP space. Most likely the owner of the IP space has created their ROAs in the Hosted RPKI interface of the RIR, which is part of their respective member portals:

* LACNIC: http://milacnic.lacnic.net/
* ARIN: https://account.arin.net/public/login
* RIPE NCC: https://my.ripe.net/
* APNIC: https://myapnic.net
* AFRINIC: https://my.afrinic.net/login

It is important to note that initially, for there to be an RPKI Invalid route, someone must have already entered into one of the above portals and made a ROA for the IP space in question.  There is no way for it to have to been done by itself.  In other words, there must already be an account at the RIR that is linked to the owner of the IP space.
