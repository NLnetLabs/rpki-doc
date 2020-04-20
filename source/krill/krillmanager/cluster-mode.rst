.. _doc_krill_manager_cluster_mode:

Cluster Mode
============

Krill Manager supports running on a cluster of servers but by default assumes
that it is not part of a cluster.

Setting up a Cluster
--------------------

---------------------
Activate Cluster Mode
---------------------

There is no support in the :ref:`doc_krill_manager_initial_setup` wizard for
activating cluster mode, instead it must be done via command line arguments to
the wizard.

After deploying N servers running Krill Manager, e.g. N instances of the
`DigitalOcean Marketplace 1-Click App <https://marketplace.digitalocean.com/apps/krill?refcode=cab39584666c>`_,
execute the following commands via SSH:

.. code-block:: bash

   # open-cluster-ports                               # on both master and slave
   # krillmanager --slave-ips=<ipv4,ipv4,...> init    # on the master only
   # krillmanager --slave=1 init                      # on the slaves only

*Example:*

.. code-block:: bash

   In a shell:
   $ ssh root@slave1.rpki.example.com
   # open-cluster-ports
   # krillmanager --slave=1 init
   ...
   Slave initialized

   In another shell:
   $ ssh root@slave2.rpki.example.com
   # open-cluster-ports
   # krillmanager --slave=1 init
   ...
   Slave initialized

   In another shell:
   $ ssh root@master.rpki.example.com
   # open-cluster-ports
   # krillmanager --slave-ips=10.0.0.2,10.0.0.3
   Joining slave at 10.0.0.2 to our GlusterFS cluster
   Joining slave at 10.0.0.3 to our GlusterFS cluster
   ...
   Waiting for all GlusterFS peers to become 'Connected'.
   ...
   Initializing Swarm manager at <some.pubic.ip.address>
   Sharing Swarm join token via GlusterFS
   Waiting for 2 swarm workers to be in status 'Ready'
   Waiting for 1 swarm workers to be in status 'Ready'
   ...

.. warning::

   ``open-cluster-ports`` is a simple helper script that opens **to the world**
   the ports required for cluster servers to communicate with each other. In a
   production setup you should restrict access so that these ports are only open
   between cluster servers and not to the wider Internet, either via ``ufw`` or
   via a cloud firewall.

----------------------------------
Deploy & Configure a Load Balancer
----------------------------------

For requests to be able to reach the Krill Manager servers, the load balancer
must be configured to forward ports:

**Port Forwarding Rules:**

+------+----------+----------------------------------------------+
| Port | Protocol | Required For                                 |
+======+==========+==============================================+
| 80   | HTTP     | - HTTP -> HTTPS redirect.                    |
|      |          | - Let's Encrypt HTTP-01 challenge responses. |
+------+----------+----------------------------------------------+
| 443  | HTTPS    | - Krill UI                                   |
|      |          | - Krill API                                  |
|      |          | - RRDP                                       |
+------+----------+----------------------------------------------+
| 873  | TCP      | - Rsync                                      |
+------+----------+----------------------------------------------+

**TLS Termination:** Either configure your load balancer with a TLS certificate
or set it to pass through TLS traffic still encrypted to the cluster servers.

**Health Check:** In order for the load balancer to route traffic only to healthy cluster servers
you should configure a :ref:`health check <faq_health_check>`.

**Proxy Protocol:** Do **NOT** enable Proxy Mode on your load balancer. See the :ref:`F.A.Q. item below <faq_proxy_protocol>`
for more information.

-------------
Configure DNS
-------------

In order to request a Let's Encrypt TLS certificate via Krill Manage the cluster
servers need to be reachable via the desired DNS name, e.g. via a DNS A or CNAME
record.

F.A.Q.
------

-----------------------
Should I Use a Cluster?
-----------------------

Whether cluster mode is needed or is the right way to achieve your objectives
depends on your particular use case. If using a 3rd party repository and only a
few ROAs, then you probably don't need a cluster.

A cluster provides various benefits including:

1. Higher availability - loss of a cluster server, whether due to an issue or
   while upgrading, does not cause the service to be down toward customers.
2. Scalability - RRDP and Rsync requests can be served by multiple servers
   instead of just one.

A cluster also comes with some costs, e.g.:

1. The obvious cost of running more (virtual) hardware.
2. The complexity cost of operating and maintaining a cluster, though Krill
   Manager greatly reduces this.

---------------------------------------------
How Is Cluster Mode Different To Normal Mode?
---------------------------------------------

The main difference is that instead of having one server running NGINX and
RsyncD, in cluster mode every cluster server will run NGINX and RsyncD.

In clustered mode the Gluster volume enables Krill Manager to replicate
configuration, TLS certificates, RRDP and Rsync repo contents, etc. to every
cluster server.

-----------------------
Why Not Just Use a CDN?
-----------------------

Currently :ref:`Relying Party software <doc_tools>` communicate with RPKI
repository servers using the Rsync protocol and most also support the RRDP
protocol.

Using a CDN (e.g. `Fastly <https://www.fastly.com/>`_ as used by the NLnet Labs
production Krill deployment) should increase availability, increase capacity and
decrease latency, but only for RRDP, not for Rsync. One could argue that Rsync
is being rapidly obsoleted by RRDP and it is only a matter of time before Rsync
is not used by Relying Parties at all.

-------------------------------------------
Where Should My Cluster Servers Be Located?
-------------------------------------------

Depending on how many `9's of uptime/availability <https://uptime.is/>`_ you are
aiming for, you should consider whether your cluster servers are separate enough
from each other, e.g. several VMs running on the same server or in the same rack
is less robust than spreading the VMs across cloud availability zones or across
regions.

Note however that the further apart your cluster servers are from each other the
longer it may take Gluster to keep the replicated volume contents consistent.

Also, not all load balancing technologies support wider separation, e.g. a cloud
load balancer may be able to balance across VMs in one region but not across
regions.

--------------------------------------------
How Can I Balance Traffic Across My Cluster?
--------------------------------------------

You can use a load balancer (e.g. the `DigitalOcean Load Balancer <https://www.digitalocean.com/products/load-balancer/>`_),
anycast IP, a CDN provider, geographic/latency based DNS, etc.

.. _faq_proxy_protocol:

----------------------------
Is `Proxy Protocol <https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt>`_ supported?
----------------------------

`Not yet <https://github.com/NLnetLabs/krillmanager/issues/2>`_. Without Proxy
Protocol you will likely see the IP addresses of the proxy in your NGINX and
RsyncD logs but rather than that of the real client.

.. _faq_health_check:

-----------------------------------------
How Can a Proxy Check the Backend Health?
-----------------------------------------

Krill Manager does not yet offer a dedicated health check endpoint. When using a
load balancer or other proxy that supports health checks you are currently
limited to testing TCP or HTTP(S) connectivity. For example if using a single
DigitalOcean Load Balancer you can check either connectivity to NGINX or to
RsyncD but not both. A dedicated Krill Manager health check endpoint would allow
you to direct traffic to the cluster server only if all services were green.

-----------------------------------------------------
What Happens If a Cluster Server Becomes Unreachable?
-----------------------------------------------------

If your proxy detects that the backend is unreachable then clients (possibly
after some delay) will no longer be routed to the "dead" server but will
continue to be able to access RRDP and Rsync endpoints on the remaining servers.

If your proxy monitors the health of the backend services and the health check
fails then connections to that service will be routed to other "healthy"
servers. Howvever, as :ref:`noted above <faq_health_check>`, the current health
check options are not perfect.

If the "unhealthy" cluster server is a slave and the "master" loses its
connection to the slave then any Krill Manager components that were running only
on that cluster server will be re-launched on a remaining "healthy" cluster
server.

If the "unhealthy" cluster server is the "master" then any Krill Manager
components that were running only on that cluster server will be lost and you
will need to manually fix the Docker Swarm and Gluster clusters. However,
note that NGINX and RsyncD run on every cluster server and so clients will still
be able to get the *last synced* RRDP and Rsync data from the remaining "healthy"
cluster servers. You may however lose Krill and/or log streaming/uploading
services.

--------------------------------------------
Can I Use Plain HTTP Behind a Load Balancer?
--------------------------------------------

No, Krill Manager does not support this.

--------------------------------------------------------------
Can I Use Self-Signed TLS Certificates Behind a Load Balancer?
--------------------------------------------------------------

In the case where the load balancer handles TLS termination, to avoid
having to install and renew real certificates on both the load balancer and the
cluster servers the ``--private`` argument can be used on the master. This will
cause Krill Manager to generate self-signed certificates for the cluster NGINX
instances. E.g.

.. code-block:: bash

   # krillmanager --slave-ips=<ipv4>,<ipv4>,... --private init

-------------------------------
How is the cluster established?
-------------------------------

1. The master server activates Docker Swarm mode becoming a Swarm Manager.
2. The master server adds the other servers as Gluster peers.
3. The master server creates a Gluster replication volume across the peers. Each
   peer will have a complete copy of the data written to the volume.
4. The master server writes the Docker Swarm join token to the Gluster volume.
5. The slave servers detect the join token and use it to join the Docker Swarm.

------------------------------------------
Can I add or remove cluster servers later?
------------------------------------------

1. Run ``open-cluster-ports`` and ``krillmanager --slave=1 init`` as usual on
   any new slave servers.
2. Run ``krillmanager --slave-ips=<ipv4>,<ipv4>,... init`` on the master
   cluster server with the new set of IPv4 cluster slave addresses:

   - Any missing slave IP addresses will cause Krill Manager to forcibly
     disconnect those slaves from the Gluster cluster.
   - Any new slave IP addresses will be added to the Gluster cluster.
   - The new slaves will then add themselves to the Swarm cluster.
3. Terminate the removed slave servers.

--------------------------------------
Is the Swarm Manager highly available?
--------------------------------------

No. This could be done but adds complexity while adding little value. If the
manager server is lost the worst case is that the Krill UI and API become
unavailable if Krill was running on the Swarm Manager server, RRDP and Rsync
endpoints will continue to be available.

--------------------------------------
Is the Docker Swarm Routing Mesh Used?
--------------------------------------

No, the NGINX (HTTP(S)/RRDP) and Rsync containers bind directly to the host
interface ensuring that IPv6 is supported and eliminating an unnecessary
extra proxy hop.
