Route Origin Validation
-----------------------

The most common routing error on the Internet is the accidental mis-origination of a prefix. This means a network operator unintentionally announces an IP prefix that they are not the holder of. An incorrect announcement can also be the result of malicious activity. Route Origin Validation answers the question:

    “*Is this particular BGP route announcement authorised by the legitimate holder of the address space?*”
    
By comparing the BGP announcement to the RPKI data set the outcome can be yes, no or undetermined. Based on this information, a network operator can decide to accept the announcement, drop it or treat it in any other way they choose. 

A similar system has been in place for many years via the Internet Routing Registry (IRR) system. However, the weakness of the IRR is that it is not a globally deployed system and it lacks the authorisation model to make the system water tight. RPKI solves these two problems, as you can be absolutely sure that an authoritative, cryptographically validatable statement can be made by any legitimate IP resource holder in the world.