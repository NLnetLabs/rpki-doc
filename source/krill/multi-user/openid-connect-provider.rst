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

Choosing a provider
-------------------

There are many identity providers that support OpenID Connect to choose
from. Some are software products that you can host yourself, others are
online services that you can create an account with.

Any OpenID Connect provider that you choose must implement the following standards:

- `OpenID Connect Core 1.0 <https://openid.net/specs/openid-connect-core-1_0.html>`_
- `OpenID Connect Discovery 1.0 <https://openid.net/specs/openid-connect-discovery-1_0.html>`_
- `OpenID Connect RP-Initiated Logout 1.0 <https://openid.net/specs/openid-connect-rpinitiated-1_0.html>`_ *(optional)*
- `RFC 7009 OAuth 2.0 Token Revocation <https://tools.ietf.org/html/rfc7009>`_ *(optional)*

Krill has been tested with the following OpenID Connect providers (in alphabetical order):

- `Amazon Cognito <https://aws.amazon.com/cognito/>`_
- `Keycloak <https://www.keycloak.org/>`_
- `Microsoft Azure Active Directory <https://azure.microsoft.com/en-us/services/active-directory/>`_
- `Micro Focus NetIQ Access Manager 4.5 <https://www.netiq.com/documentation/access-manager-45-developer-documentation/administration-rest-api-guide/data/oauth-openid-connect-api.html>`_

.. warning:: Krill has been verified to be able to login and logout with `Google Cloud <https://developers.google.com/identity/protocols/oauth2/openid-connect>`_
             accounts. However, it is not advisable to grant access to
             Google accounts in general. Instead you should use a
             Google product that permits you to manage your own pool of
             users so that you can restrict access to just these users.
             Additionally, if you wish to assign different Krill rights
             to different users you will need some way to mark the
             users to indicate which role they should receive, e.g. by
             grouping them or `configuring custom claims <https://cloud.google.com/identity-platform/docs/how-to-configure-custom-claims>`_.

Setting it up
-------------

The following steps are required to use local users in your Krill setup.

.. note:: The manner of configuring the provider varies greatly between
          providers. We will use `Keycloak <https://www.keycloak.org/>`_
          for the example below. Some basic tips for known providers are
          given in ``krill.conf`` along with complete documentation for
          all of the possible configuration options. You will need to
          consult the documentation for your provider to see how to
          carry out steps similar to those given below and to decide
          which ``krill.conf`` configuration settings and values are
          correct for your situation.

1. Decide on the settings to be configured.
"""""""""""""""""""""""""""""""""""""""""""

Decide which usernames you are going to configure, and what :ref:`role <doc_krill_multi_user_access_control>`
and password they should have. For this example let's assume we want to
configure the following users:

================= ======== =========
Username          Password Role
================= ======== =========
joe@example.com   dFdsapE5 admin
sally             wdGypnx5 readonly
dave_the_octopus  qnky8Zuj readwrite
================= ======== =========

And let's assume that we are going to use a local Docker `Keycloak <https://www.keycloak.org/>`_
container as our OpenID Connect provider.

----

2. Configure the provider
"""""""""""""""""""""""""

Let's walk through configuring the provider step by step:

.. contents::
  :local:
  :depth: 1


Download and run Keycloak
~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   $ sudo docker run \
       --detach \
       --name keycloak \
       --publish 8443:8443 \
       --env KEYCLOAK_USER=admin \
       --env KEYCLOAK_PASSWORD=password \
       --env DB_VENDOR=h2 quay.io/keycloak/keycloak:12.0.4

Follow the logs until Keycloak is ready:

.. code-block:: bash

   $ docker logs --follow keycloak
   ...
   14:31:20,766 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0025: Keycloak 12.0.4 (WildFly Core 13.0.3.Final) started in 23954ms - Started 687 of 972 services (687 services are lazy, passive or on-demand)
   14:31:20,768 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0060: Http management interface listening on http://127.0.0.1:9990/management
   14:31:20,769 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0051: Admin console listening on http://127.0.0.1:9990

Login to the Keycloak admin UI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Browse to https://localhost:8443/.
- Accept the self-signed TLS certificate.
- Click on `Administration Console`.
- Login as user `admin` password `password`.

Create a realm
~~~~~~~~~~~~~~

- Hover over `Master` in the top left and click on the `Add Realm`
  button that appears.
- Set the field values as follows then click `Create`:

  ===================  ======================================
  Field                Value
  ===================  ======================================
  Name                 `krill`
  ===================  ======================================

Create a client application
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip:: This is where we register Krill with the OpenID Connect provider.

Continuing in the KeyCloak web UI with realm set to `krill`:

- Click `Clients` (top left) then `Create` (top right).
- Set the field values as follows then click `Save`:

  ===================  ======================================
  Field                Value
  ===================  ======================================
  Client ID            `krill`
  ===================  ======================================

- On the `Settings` tab that is shown next set the field values as
  follows then click `Save` at the bottom.

  ===================  ======================================
  Field                Value
  ===================  ======================================
  Consent Required     `ON`
  Access Type          `confidential`
  Valid Redirect URIs  `https://localhost:3000/auth/callback`
  ===================  ======================================

- Generate credentials for Krill to use:

  - Open the `Credentials` tab (at the top).
  - Copy the `Secret` value somewhere safe, we'll need it later.

Configure a role mapper
~~~~~~~~~~~~~~~~~~~~~~~

.. tip:: This is where we create custom claims that Krill can detect and
         use to determine which rights in Krill to assign to the user.

- Open the `Mappers` tab (at the top) and then click `Create`.
- Set field values as follows then click `Save` at the bottom:

  =====================  ======================================
  Field                  Value
  =====================  ======================================
  Name                   `krill_role`
  Mapper Type            `User Attribute`
  User Attribute         `role`
  Token Claim Name       `role`
  Claim JSON Type        `String`
  =====================  ======================================

Create the users
~~~~~~~~~~~~~~~~

- Click `Users` (on the left) then click `Add User` (top right).
- Set field values as follows then click `Save` at the bottom:

  =====================  ======================================
  Field                  Value
  =====================  ======================================
  Username               `<THE USERS NAME>`
  =====================  ======================================

- Open the `Credentials` tab and set the field values as follows:

  =====================  ======================================
  Field                  Value
  =====================  ======================================
  Password               `<THE USERS PASSWORD>`
  Password Confirmation  `<THE USERS PASSWORD>`
  =====================  ======================================

- Set `Temporary` to `OFF`.
- Click `Set Password`.
- When asked `"Are you sure you want to set a password for this user?"` click `Set password`.

- Open the `Attributes` tab.

  - Enter Key `role` with value `readonly` and press `Add`.
  - Click `Save` at the bottom.

Repeat the above adding the other users.

----

3. Configure Krill
""""""""""""""""""

Add the following to your ``krill.conf`` file: *(remove or comment out
any existing ``auth_type`` line)*

.. code-block:: none

   auth_type = "openid-connect"
   
   [auth_openidconnect]
   issuer_url = "https://localhost:8443/auth/realms/krill"
   client_id = "krill"
   client_secret = "<SECRET VALUE SAVED EARLIER>"

   [auth_openidconnect.claims]
   id = { jmespath="preferred_username" }   

----

4. Go!

Restart Krill and browse to the Krill web user interface. Your
users should now be able to login with the Keycloak login form.

.. image:: img/keycloak-krill-login.png

Once logged in your users should have the role that you assigned
to them:

.. image:: img/keycloak-user-properties-in-krill.png