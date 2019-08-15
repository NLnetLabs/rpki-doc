.. _doc_krill_pub_api:

.. WARNING::  This part of the project is currently being built. 
              Documentation will likely change significantly as the software evolves.

API
===

This application uses a JSON based REST (in the non-religious interpretation)
API for managing all administrative tasks, such as managing the configured
publishers.

General
-------

The forthcoming uswr  and CLI use this API exclusively, i.e. there are no back doors
being used. You can, of course, use this API directly from your own 
applications, or wrap things in your own UI if you want.

The API path includes a version. The idea is that we may add functionality, but
will not introduce breaking changes to existing functionality. You may expect
additional resources, and you may see additional data (json members) within 
resources. So, please make sure that you ignore what you don't understand 
when using this API.

The base uri for the API is:
https://localhost:3000/api/v1/

.. Important::  Calls to the API have to include the API token as an `OAuth 2.0 
                Bearer token <https://tools.ietf.org/html/rfc6750#section-2.1>`_ as a
                header, e.g.:
                
                ``Authorization: Bearer secret``
                
Error Responses
---------------

The API may have to return errors. When this happens the generic response 
will have an HTTP status code, and include a json message with the following 
generic structure:

.. code-block:: bash

    { "code": <int>, "msg": "Specific error message"}


There are three categories of errors, each with their own HTTP status code 
and range of error codes:

=============  ============  =====
Category        Code Range   HTTP  
=============  ============  =====
User Input       1000-1999    400
Authorisation    2000-2999    403
Server Error     3000-3999    500
=============  ============  =====

Note however that this applies only the "admin" API. The `publication 
procotol <https://tools.ietf.org/html/rfc8183>`_, and (in the future) `provisioning 
protocol <https://tools.ietf.org/html/rfc6492>`_, have their own defined ways 
of dealing with errors. 

API End Points
--------------

This section contains an overview of all API calls and specifies which errors may be expected for each. 

We will discuss all the API calls below, and we will mention which errors may
be expected for each. An example CLI call is included wherever an API end-point
is documented.

Health Check
""""""""""""

Path
++++

.. code-block:: bash

    /api/v1/health

Success Reply
+++++++++++++

HTTP code 200, empty body

Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
403    —     Forbidden (wrong token)
500    json  Some server issue
=====  ====  =======================

CLI Example
+++++++++++

.. code-block:: bash

    krill_admin --server https://localhost:3000/ --token secret health

The exit code will be 0 if everything is okay, or 1 otherwise. There is no 
text output, except when errors occur.

Publishers
""""""""""

Publishers are entities who are allowed to publish content using this 
publication server, as described in RFC 8181. Typically publishers will be 
RPKI Certificate  Authorities, however we also include a ``pubc`` binary that can
act as a publisher, and that can synchronise any arbitrary directory with the
publication server.

The following 'publishers' end points are defined:

==========================================  ========  =================================
Resource                                     Method    Action
==========================================  ========  =================================
/api/v1/publishers                           GET       List all current publishers
/api/v1/publishers                           POST      Submit a new `publisher request 
                                                       <https://tools.ietf.org/html/rfc8183#section-5.2.3>`_
/api/v1/publishers/{handle}                  GET       Show publisher details
/api/v1/publishers/{handle}/id.cer           GET       Get publisher id certificate
/api/v1/publishers/{handle}/response.xml     GET       Get `repository response.xml 
                                                       <https://tools.ietf.org/html/rfc8183#section-5.2.4>`_
==========================================  ========  =================================


List Publishers
"""""""""""""""

Path
++++

.. code-block:: bash

    /api/v1/publishers (GET)

Success Reply Example
+++++++++++++++++++++

.. code-block:: json

	{
	  "publishers": [
		{
		  "id": "alice",
		  "links": [
			{
			  "rel": "response.xml",
			  "link": "/api/v1/publishers/alice/response.xml"
			},
			{
			  "rel": "self",
			  "link": "/api/v1/publishers/alice"
			}
		  ]
		}
	  ]
	}

Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
403    —     Forbidden (wrong token)
500    json  Some server issue
=====  ====  =======================

CLI Example
+++++++++++

.. code-block:: bash

    krill_admin --server https://localhost:3000/ --token secret publishers list


Add a Publisher
"""""""""""""""

Path
++++

.. code-block:: bash

    /api/v1/publishers (POST)


Post body: `'publisher request' XML file' <https://tools.ietf.org/html/rfc8183#section-5.2.3>`_

Success Reply
+++++++++++++

HTTP code 200, empty body

Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
400    json  Issue with input
403    —     Forbidden (wrong token)
500    json  Some server issue
=====  ====  =======================

For the 400 errors you can expect the following error messages:

======  ===================================  ============================================
Code    Description                          Code Module
======  ===================================  ============================================
1002    Invalid RFC8183 Publisher Request    PublisherRequestError
1004    Forward slash in publisher handle    publishers::Error::ForwardSlashInHandle
1005    Duplicate publisher handle           publishers::Error::DuplicatePublisher
======  ===================================  ============================================

CLI Example
+++++++++++

.. code-block:: bash

   krill_admin --server https://localhost:3000/ --token secret publishers add --xml work/tmp/alice.xml

Publisher Details
"""""""""""""""""

Path
++++

.. code-block:: bash

    /api/v1/publishers/{handle} (GET)  

Success Reply Example
+++++++++++++++++++++

**TODO**

Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
403    —     Forbidden (wrong token)
404    —     Unknown Publisher
500    json  Some server issue
=====  ====  =======================

CLI Example
+++++++++++

**TODO**

Publisher Identity Certificate
""""""""""""""""""""""""""""""

Path
++++

.. code-block:: bash

    /api/v1//publishers/{handle}/id.cer  (GET)  

Success Reply
+++++++++++++

The X509 Identity Certificate this publisher uses to sign CMS messages used 
in the publication and provisioning protocol.


Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
403    —     Forbidden (wrong token)
404    —     Unknown Publisher
500    json  Some server issue
=====  ====  =======================

CLI Example
+++++++++++

**TODO**

Publisher Response
""""""""""""""""""

Gets the `repository response.xml <https://tools.ietf.org/html/rfc8183#section-5.2.4>`_
for the specified publisher.

Path
++++

.. code-block:: bash

    /api/v1/publishers/{handle}/response.xml


Success Reply Example
+++++++++++++++++++++

**TODO**

Possible Error Replies
++++++++++++++++++++++

=====  ====  =======================
http   body  description
=====  ====  =======================
403    —     Forbidden (wrong token)
404    —     Unknown Publisher
500    json  Some server issue
=====  ====  =======================

CLI Example
+++++++++++

**TODO**

Overview of API Errors
----------------------

User Input Codes
""""""""""""""""

The following user input errors may be returned:

======  ======================================  ========================================
Code    Description                             Code module
======  ======================================  ========================================
1001    Submitted Json cannot be parsed         serde_json::Error
1002    Invalid RFC8183 Publisher Request       PublisherRequestError
1003    Issue with submitted publication XML    pubmsg::MessageError
1004    Forward slash in publisher handle       publishers::Error::ForwardSlashInHandle
1005    Duplicate publisher handle              publishers::Error::DuplicatePublisher
1006    Unknown publisher                       publishers::Error::UnknownPublisher
======  ======================================  ========================================

Authorisation Codes
"""""""""""""""""""

The following authorisation errors may be returned:

======  ==========================================  ===================================
Code    Description                                 Code module
======  ==========================================  ===================================
2001    Submitted protocol CMS does not validate    pubserver::Error::ValidationError
======  ==========================================  ===================================

Server Error Codes
""""""""""""""""""

The following server errors may be returned. These errors indicate that there
 is a bug, or operational issue (e.g. a disk cannot be written to) at the 
 server side:
 
======  ==========================================  ======================================
Code    Description                                 Code module
======  ==========================================  ======================================
3001    Issue with storing/retrieving publisher     pubserver::Error::PublisherStoreError
3002    Issue with updating repository              pubserver::Error::RepositoryError
3003    Issue with signing response CMS             pubserver::Error::ResponderError
======  ==========================================  ======================================


