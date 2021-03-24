.. _doc_krill_multi_user_custom_policies:

Custom Authorization Policies
=============================

.. versionadded:: v0.9.0

.. contents::
  :local:
  :depth: 2

Introduction
------------

Custom authorization policies are a way of extending Krill by supplying
one or more files containing rules that will be added to those used by
Krill when deciding if a given action by a user should be permitted or
denied.

Examples
--------

Some examples showing the power of this can be seen in `doc/policies <https://github.com/NLnetLabs/krill/doc/policies>`_
directory in the Krill source code repository.

`role-per-ca-demo`
""""""""""""""""""

By default Krill lets you assign a role to a user that will be enforced
for all of the actions that they take irrespective of the CA being
worked with. The `role-per-ca-demo` example extends Krill so that a
user can be given different roles for different CAs.

The demo also shows how to use new user attributes to influence
authorization decisions, in this case by looking for a user attribute
by the same name as the CA being worked with, and if found it uses the
attribute value as the role that the user should have when working with
that CA.

Finally, the demo demonstrates how to add new roles to Krill by adding
two new roles that are more limited in power than the default roles in
Krill:

  - A `readonly`-like role that also has the right to update ROAs.
  - A role that only permits a user to login and list CAs.

`team-based-access-demo`
""""""""""""""""""""""""

The `team-based-access-demo` shows how one can define teams in the
policy:

  - Users can optionally belong to a team.
  - Users can have a different role in the team than outside of it.
  - Being a member of a team grants access to the CAs that the team
    works with.

The example works by defining the team names in the policy file (as
there is no way to define something new that is independent of Users
in the config file, you can only add new attributes or claim mappings,
not non-user related properties).

Each team is given a name and a list of CAs it works with. Krill is
then extended to understand two new user attributes:

  - `team` - which team a user belongs to
  - `teamrole` - which role the user has in the team

Using custom policies
---------------------

To use a custom policies there must be an ``auth_policies`` setting
in ``krill.conf`` specifying the path to one ore more custom policy
files to load on startup.

.. code-block:: none

   auth_type = "..."
   auth_policies = [ "doc/policies/role-per-ca-demo.polar" ]

.. warning:: Krill will fail to start if a custom authorization
             policy file is syntactically invalid or if one of the
             self-checks in the policy fails.

.. warning:: Policy files should only be readable by Krill and
             trusted operating system user accounts.
             
             Krill performs some basic sanity checks on startup to
             verify that its authorization policies are working as
             expected, but a malicious actor could make more subtle
             changes to the policy logic which may go undetected,
             like granting their own user elevated rights in Krill.

             If a malicious user is able to write to the policy
             file they may however already be able to do much more
             significant damage than editing a policy file!

.. note:: Policy files are not reloaded if changed on disk while
          Krill is running.

          For policies that only contain rules this is not a
          problem as the rules would not be expected to change
          very often, if ever.

          However, for policies that define configuration in the
          policy file, such as the `team-based-access-demo`,
          changes to the policy configuration will not take effect
          until Krill is restarted.

Writing custom policies
-----------------------

TO DO