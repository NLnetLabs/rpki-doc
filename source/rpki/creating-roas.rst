.. _doc_rpki_roas:

Creating Route Origin Authorisations
====================================

A Route Origin Authorisation object consists of three elements: the AS Number that you authorise, the prefix that is being originated from it and, lastly, the Maximum Length (MaxLength), which determines the most specific prefix that the AS may originate out of the aggregate. Keep in mind that a single ROA makes the announcement of a prefix from an authorised AS valid, but at the same time, it makes the announcement from an unauthorised (hijacking) AS invalid. You should create as many ROAs as needed to make all legitimate announcements valid.

There are several ways to authorise a BGP origin with a ROA. For example, you can authorise the entire aggregate to be originated – including all more specific announcements – with a single ROA. Or you can authorise each announcement with a ROA individually. While creating a single ROA seems convenient, it is not recommended. 

For example, if you are the holder of 10.0.0.0/18 and you announce the aggretate as well as several more specifics up to a /24 from AS64500, you could create a ROA for AS64500 authorising 10.0.0.0/18 and set the MaxLength to /24. However, if an adversary now spoofs your ASN and starts announcing any more specific out of your aggregate, they will all be seen as RPKI VALID, because there is a covering ROA for it.

The best practice is to create a ROA for each announcement individually, and set the MaxLength to exactly the prefix length that you actually announce.
