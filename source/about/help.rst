RPKI for people who don't know anything about it

If you're reading this page, chances are you find yourself in a situation where you've been told by someone that your routes have invalid ROAs and you don't know what that means.  The aim of the content on this page will point you in the right direction and provide further resources that can be of assistance.  This page is not meant for experts, and many technicalities will be glossed over in order to be able to provide easy to understand answers for all knowledge levels.

What is RPKI or ROA?
RPKI stands for Resource Public Key Infrastructure, ROA stands for Route Authorization Object.

What do they do?
They provide a method for the originator of a route to  assert they are the correct originator and that other originators are not valid.

How does it work?
The "root" assigner of all IP space (v4+v6) is IANA.  They have delegated this space to one of the RIRs (ARIN,RIPE,APNIC,LACNIC, and AFRINIC).  In turn, those RIRs assign the space to other entities. Each RIR has a portal where the owner of the space can assert the origination ASN, which then generates a ROA for that particular combination of route and origination ASN.  This ROA is then published out by the RIR so that anyone can view them.

What happens next?
