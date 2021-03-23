.. _doc_krill_multi_user_openid_connect_provider:

OpenID Connect Users
====================

.. versionadded:: v0.9.0

.. contents::
  :local:
  :depth: 1

Introduction
------------

`OpenID Connect <https://openid.net/connect/>`_ is a widely supported
standard that builds on the OAuth 2.0 standard to authenticate users
and provide basic profile information about those users.

The user visible part of the login experience when using OpenID Connect is
handled by the OpenID Connect provider and may look quite different to the
Krill web user interface:

.. figure:: img/openid-connect-login.png
    :align: center
    :width: 100%
    :alt: Azure ActiveDirectory login screen

    Enter Azure Active Directory credentials to access Krill

To use OpenID Connect Users in Krill you will either need to run your own
OpenID Connect provider or use one provided by a 3rd party service
provider.

Why OpenID Connect?
"""""""""""""""""""

From the `OpenID Connect FAQ <https://openid.net/connect/faq/>`_:

  **What problem does OpenID Connect solve?**

  *It lets app and site developers authenticate users without taking on the
  responsibility of storing and managing passwords in the face of an
  Internet that is well-populated with people trying to compromise your
  users’ accounts for their own gain.*

OpenID Connect takes the lessons learned from earlier identity protocols
and improves on them. It is `widely implemented <https://openid.net/developers/certified/>`_
and deployed, and for situations where the primary identity provider does
not implement OpenID Connect there are OpenID Connect providers that can
act as a bridge to systems that implement other identity protocols.

As a modern, tried & tested and widely implemented protocol it is therefore
quite likely that it is either already in use by (potential) Krill
operators or viable for them to adopt.

Why not OAuth 2.0?
"""""""""""""""""""

From https://oauth.net/articles/authentication/:

  **OAuth 2.0 is not an authentication protocol.**

  *Much of the confusion comes from the fact that OAuth is used inside of
  authentication protocols, and developers will see the OAuth components
  and interact with the OAuth flow and assume that by simply using OAuth,
  they can accomplish user authentication. This turns out to be not only
  untrue, but also dangerous for service providers, developers, and end
  users.*

How does it work?
-----------------

Let's assume that the OpenID Connect provider is compatible with Krill and
that Krill has been registered with the provider (see below for more on
these topics).

The user experience
"""""""""""""""""""

When an end user visits the Krill website in their browser they will be
redirected to the login page of the OpenID Connect provider. This is
**NOT** part of Krill.

For example, when logging in to a Krill instance connected to the OpenID
Connect provider in a large company, the end user might see a very familiar
login page. That's becausae it is probably a page they have to login to in
order to use many other services in their company. Often this login page
will even be themed to match the corporate branding.

The user enters **their** credentials into the OpenID Connect provider
login page. At this point Krill knows nothing about who is logging in at
the provider login form.

.. tip:: Krill **NEVER** receives the username or password that the user
         enters in to the OpenID Connect provider login page and Krill has
         no control over the appearance and/or behaviour of the OpenID
         Connect provider login page.

If the login is successful, from the users perspective their browser is
then directed back to Krill where they see the Krill web user interface as
if they are logged in. Krill will provide the web user interface with a
token which the web user interface should send on subsequent requests to
authenticate itself with Krill. The web user interface will keep a copy of
this token in browser local storage until the user logs out or is timed
out due to inactivity.

Krill will honour any session expiration time communicated to it by the
OpenID Connect provider. When using OpenID Connect Users it is therefore
possible that the user will be informed that they cannot perform the
requested action because their login session has timed out and they need
to login again. Where possible Krill will automatically extend the login
session to avoid this happening.

In the background
"""""""""""""""""

What the user doesn't see, except perhaps if their network connection is
very slow, is that there are "hidden" intermediate steps occuring in the
login flow, between the browser and Krill and between Krill and the OpenID
Connect provider. These steps implement the OpenID Connect `"Authorizaton
Code Flow" <https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>`_.

If the user logged in correctly at the OpenID Connect provider login page
and Krill was correctly registered with the provider and the provider was
correctly setup for Krill, then Krill will receive a temporary Authorization
Code which it exchanges for an OAuth 2.0 `Access Token <https://www.oauth.com/oauth2-servers/access-tokens/>`_
(and maybe also an OAuth 2.0 Refresh Token) and an OpenID Connect ID Token.

The ID Token includes so-called OAuth 2.0 **claims**, metadata about the
user logging in. These claims are the key to whether or not Krill is able
to determine which rights, if any, to grant to the user that is attempting
to login.

Known limitations
-----------------

OpenID Connect Users avoid the problems with :ref:`Config File Users <doc_krill_multi_user_config_file_provider>`
but requires more effort to setup and maintain:

- Requires operating another service or using a 3rd party service.
- Confguring Krill and the OpenID Connect provider is more involved than
  setting up :ref:`Config File Users`.
- If Krill cannot contact the OpenID Connect provider, users will be
  unable to login to Krill with their OpenID Connect credentials. It will
  however still be possible to authenticate with Krill using its secret
  token.
