.. _doc_krill_pub_api:

API
---

This application uses a JSON based REST (in the non-religious interpretation)
API for managing all administrative tasks, such as managing the configured
publishers.

The UI and CLI (to be) included with this application (will) use this API 
exclusively, i.e. there are no back doors being used. You can, of course, use
this API directly from your own applications, or wrap things in your own UI 
if you want.

The API path includes a version. The idea is that we may add functionality, but
will not introduce breaking changes to existing functionality. You may expect
additional resources, and you may see additional data (json members) within 
resources. So, please make sure that you ignore what you don't understand 
when using this API.

The base uri for the API is:

..

   http://localhost:3000/api/v1/

*NOTE:* Calls to the API have to include the api token as an `OAuth 2.0 
Bearer token <https://tools.ietf.org/html/rfc6750#section-2.1>`_ as a header, e.g.:

..

    Authorization: Bearer secret