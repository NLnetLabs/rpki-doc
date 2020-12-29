.. _doc_krill_failure_scenarios:

Failure Scenarios
=================

.. Important:: The most important thing to remember about possible failure 
               scenarios in your setup is that Krill is designed to run
               continuously, but there is no strict uptime requirement for the
               Certificate Authority (CA). If the CA is not available you just
               cannot create or update ROAs. As long as your existing ROAs are
               being served by your web server and rsync server, you have time
               to solve problems.

As long as Krill is running, it will automatically update the entitled resources
on your certificate, as well as reissue certificates, ROAs and all other objects
before they expire or become stale. If Krill does go down, you have 8 hours to
bring it back up before data starts going stale.

Krill Certificate Authority
---------------------------

Failure of the Primary Krill CA Server
""""""""""""""""""""""""""""""""""""""

When the primary CA server goes down, users will not be able to create or edit
ROAs until the server is back up or failover to a secondary server has been
completed. Relying party software will continue to be able to download and
verify existing ROAs. 

With default timing settings new manifests and certificate revocation lists
(CRLs) will be published eight hours before they would expire, with a validity
time of 24 hours. This means that the CA should be restored within eight hours
after failure. Please note that values can be changed. For example, it would be
perfectly fine to republish 16 hours before expiry, or even longer if the
validity time is also extended. However, we do not recommend extending the
validity time beyond 24 hours because it could allow (theoretical) attacks where
data is replayed to validators for longer.

The Disk on the Primary Krill CA Server is Full
"""""""""""""""""""""""""""""""""""""""""""""""

Krill will crash, by design, if there is any failure in saving any state file to
disk. If Krill cannot persist its state it should not try to carry on. It could
lead to disjoints between in-memory and on-disk state that are impossible to
fix. Therefore, crashing and forcing an operator to look at the system is the
only sensible thing Krill can now do.

If this occurs, more space should be allocated. Krill can then try to restart
properly. It will try to go back to the last possible recoverable state if:

* it cannot rebuild its state at startup due to data corruption
* the environment variable: ``KRILL_FORCE_RECOVER`` is set
* the configuration file contains ``always_recover_data = true``

Under normal circumstances performing this recovery will not be necessary. It
can also take significant time due to all the checks performed. So, we do **not
recommend** forcing recovery when there is no data corruption.

See :ref:`Recover State at Startup<recover_state_startup>` for more details.

During this failure, Relying party software will continue to be able to download
and verify existing ROAs. 

Corruption of Files on the Primary Krill CA Server
""""""""""""""""""""""""""""""""""""""""""""""""""

If an administrator or malicious attacker with access to the server modifies
files after Krill has written them, then Krill will just continue to operate. It
will not re-read files as it keeps all data in memory as well. However, Krill
may be unable to come up if it is restarted. By default Krill will then only
read the latest snapshots of its state components and modifications since then.
Krill will start if those files are unaffected.

Similar to the situation with a full disk, Krill will try to go back to the last
possible recoverable state if:

* it cannot rebuild its state at startup due to data corruption
* the environment variable: ``KRILL_FORCE_RECOVER`` is set
* the configuration file contains ``always_recover_data = true``

See :ref:`Recover State at Startup<recover_state_startup>` for more details.

Krill can also be restored from a backup, but it would result in losing all
changes from after the backup. As described above, if Krill finds that the
backup contains an incomplete transaction, it will fall back to the state prior
to it. 

During this failure, Relying party software will continue to be able to download
and verify existing ROAs. 

An Incorrect ROA is Published for the Repository Servers
""""""""""""""""""""""""""""""""""""""""""""""""""""""""

When one or more ROAs are generated that cause the RPKI repository prefix to be
considered *RPKI INVALID*, Relying Party software will retrieve these ROAs and
promptly those networks will drop the repository prefix. This means that even
if/when operators fix the ROAs, the validators will not be able to retrieve the
updated information until their cached manifest and CRLs go stale. This issue
can persist for a minimum of 8 hours and a maximum of 24 hours.

Krill Publication Server
------------------------

The Disk on the Krill Publication Server is Full
""""""""""""""""""""""""""""""""""""""""""""""""

Krill will crash, by design, if there is any failure in saving any state file to
disk. If this occurs, more space should be allocated. Krill can then try to
restart properly. It will try to go back to the last possible recoverable state
as described in the :ref:`Recover State at Startup<recover_state_startup>`
section.

If the repository is restored from back up, Relying Party software may be served
outdated content from that backup for a short while. However, when the service
is resumed then the Krill CA will do a full re-sync and publish its current
content within 5 minutes if it had pending unpublished changes. If not, then a
manual re-sync can be triggered through the user interface, the command line or
the API.

During this failure, Relying party software will continue to be able to download
and verify existing ROAs. 

Failure of one of the Repository Servers
""""""""""""""""""""""""""""""""""""""""

When a repository server running the web server and/or rsyncd goes down or the
service dies, the load balancer should mitigate the issue. An error should be
generated and the failed instance should be taken out of the pool. During this
failure, Relying party software will continue to be able to download and verify
ROAs from the other repository servers that are configured. 

