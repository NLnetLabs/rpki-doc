.. _doc_krill_multi_user_openid_connect_provider:

OpenID Connect Users
====================

.. versionadded:: v0.9.0

`OpenID Connect <https://openid.net/connect/>`_ is a widely supported
standard that builds on the OAuth 2.0 standard to authenticate users
and provide basic profile information about those users.

The user visible part of the login experience when using OpenID Connect is
handled by the OpenID Connect provider and may look quite different to the
Krill web ser interface:

.. figure:: img/openid-connect-login.png
    :align: center
    :width: 100%
    :alt: Azure ActiveDirectory login screen

    Enter Azure ActiveDirectory credentials to access Krill

To use OpenID Connect you will either need to run your own OpenID Connect
provider or an existing OpenID Connect provider in your organization or
offered by a 3rd party.

How does it work?
-----------------

1. Registration
"""""""""""""""

To setup the connection with the OpenID Connect provider Krill requires
three things:

- The domain name at which your Krill instance is available to your users
  via their browser.
- The OpenID Connect provider `issuer discovery URL <https://openid.net/specs/openid-connect-discovery-1_0.html#IssuerDiscovery>`_.
- Client credentials issued to you (in the form of a client ID and client
  secret) by the OpenID Connect provider for use by your Krill instance.

To obtain the client credentials you will first need to register Krill with
the provider and configure it with the callback URL of your Krill instance, 
which must be reachable by your users in their browser.

2. User configuration
"""""""""""""""""""""

Using the management interface of your OpenID Connect provider you will
need to ensure that it is configured to provide Krill with the required
user details, known in OpenID Connect/OAuth 2.0 as "claims".

If for example your provider contains thousands of users for your company
and you do not wish them all to be able to login to your Krill instance
you will need some way to decide which Krill rights to grant to which 
users when they attempt to login. This could be some existing property
of the users (they might all be members of a group called "Krill Users"
or "RPKI Operators" for example). Alternatively you might need to tag
them in some way in the provider such that they can be recognized by Krill
(e.g. by adding a "krill_role" property to them).

You will then need to configure Krill to be able to detect this
recognizable property of the users that you have decided to filter on.

3. Provider login
"""""""""""""""""

When a user browses to the Krill web user interface URL they will be
immediately redirected to the login page of the OpenID Connect provider.

Once the provider has verified their login credentials it will redirect
the users browser to a Krill "callback" URL with a temporary code.

4. Krill login
""""""""""""""

At this point the user is in the middle of being logged in to the OpenID
Connect property and is NOT yet logged in to Krill at all.

First Krill must contact the provider directly to exchange the temporary
code for so-called Access Token and ID Tokens. The "claims" that we
referred to above will be made available to Krill as part of the ID
Token. Krill will then use its configuration to inspect the claims in
order to find the information it needs to assign a Krill role to the
user.

If no role can be determined then Krill will respond to the redirect
with an error and the user will be shown an error. Otherwise Krill will
redirect the user to the Krill login success page complete with a Krill
specific login token. At this point the user is logged in to Krill AND
to the OpenID Connect provider.

The web user interface will keep a copy of the Krill token in browser
local storage and will send it on subsequent requests to authenticate
itself with Krill. The browser will delete the stored token either until
the user logs out or is timed out due to inactivity by the web user
interface.

5. OpenID Connect token expiration and refresh
""""""""""""""""""""""""""""""""""""""""""""""

If the OpenID Connect provider indicated that the token it issued will
expire at a particular time, Krill will respect that and will deny any
request from the web user interface after that time. If the OpenID
Connect provider also issued a Refresh Token, when Krill is contacted
by the Krill web user interface it will attempt to refresh the session
it has with the OpenID Connect provider which if successful will cause
Krill to wait until the new expiration moment before responding to a
Krill web user interface request with an access denied error.

Supported providers
-------------------

Krill has been tested with (in alphabetical order):

- `Amazon Cognito <https://docs.aws.amazon.com/cognito/index.html>`_
- `Google Cloud <https://cloud.google.com/anthos/clusters/docs/on-prem/1.6/how-to/oidc>`_
- `RedHat Keycloak <https://www.keycloak.org/>`_
- `MicroFocus NetIQ Access Manager 4.5 <https://www.netiq.com/documentation/access-manager-45-developer-documentation/administration-rest-api-guide/data/oauth-openid-connect-api.html>`_
- `Microsoft Azure Active Directory <https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/auth-oidc>`_

.. tip:: Some providers are also able to act as an intermediary between
         different identity provider systems and may be able to offer the
         OpenID Connect interface needed by Krill while the users actually
         exist in a completely different provider.

This provider supports multiple users each with their own properties.

- Authentication is handled by the `OpenID Connect 1.0 Core <https://openid.net/specs/openid-connect-core-1_0.html>`_
  (OIDC) compliant identity provider service. Krill has neither knowledge of nor
  access to user credentials.
- Authorization is handled by Krill based on Role and Resource
  Restrictions information, either extracted from OIDC service responses, or
  defined locally in the Krill configuration file.
- The ID of the logged in user shown in the web user interface and recorded in the Krill
  event history need not be the username or other ID used to login to the OIDC
  provider, it can be set to any value reported by the OIDC provider.

**THIS DOCUMENT IS A WORK IN PROGRESS. CHECK BACK HERE LATER FOR MORE CONTENT.**