.. _doc_krill_manager_cluster_mode:

Cluster Mode
============

Krill Manager supports running on a cluster of servers but by default assumes
that it is not part of a cluster.

Should I use a cluster?
-----------------------

If using a 3rd party repository and only a few ROAs, then probably not. The only
service that you operate in this case is Krill itself and you may be okay with
Krill being unavailable for short periods and likely do not expect to place much
load on Krill or serve many requests.

A cluster provides various benefits including:

1. Higher availability - loss of a cluster server, whether due to an issue or
   while upgrading, does not cause the service to be down toward customers.
2. Scalability - RRDP and Rsync requests can be served by multiple servers
   instead of just one.

A cluster also comes with some costs, e.g.:

1. The obvious cost of running more (virtual) hardware.
2. The complexity cost of operating and maintaining a cluster, though Krill
   Manager greatly reduces this.

Why not just use a CDN?
-----------------------

Whether cluster mode is needed or is the right way to achieve your objectives
depends on your particular use case. For example, higher availability, increased
scale AND lower latency can be achieved by using a Content Delivery Network such
as `Fastly <https://www.fastly.com/>`_, but only for RRDP, not for Rsync. One
could argue that Rsync is being rapidly obsoleted by RRDP and it is only a
matter of time before Rsync is not used by Relying Parties at all.

Depending on how many `9's of uptime/availability <https://uptime.is/>`_ you are
aiming for, you should also consider whether your cluster servers are separate
enough from each other, e.g. several VMs running on the same server or in the
same rack is less robust than spreading the VMs across cloud availability zones
or regions.

If a cluster is what you need, Krill Manager supports it.

Activating cluster mode
-----------------------

There is no support in the :ref:`doc_krill_manager_initial_setup` wizard for
activating cluster mode, instead it must be done via command line arguments to
the wizard.

After deploying N servers running Krill Manager, e.g. N instances of the
`DigitalOcean Marketplace 1-Click App <https://marketplace.digitalocean.com/apps/krill?refcode=cab39584666c>`_:

On one server (aka the master) run these commands:

.. code-block:: bash

   # open-cluster-ports
   # krillmanager --slave-ips=<ipv4,ipv4,...> init

Where each ``<ipv4>`` value is the IPv4 address of one of the N-1 slave servers
in the cluster.

On the other N-1 servers in the cluster (aka the slaves) run these commands:

.. code-block:: bash

   # open-cluster-ports
   # krillmanager --slave=1 init

.. warning::

   ``open-cluster-ports`` is a simple helper script that opens **to the world**
   the ports required for cluster servers to communicate with each other. In a
   production setup you should restrict access so that these ports are only open
   between cluster servers and not to the wider Internet, either via ``ufw`` or
   via a cloud firewall.

What else do I need to do?
--------------------------

Krill Manager cannot ensure that your DNS records and load balancer are setup,
you must take care of that yourself.

In order to request a Let's Encrypt TLS
certificate via Krill Manage in cluster mode you will either need to use the
same A record name for each cluster server, or set the external A or CNAME
record to point at your load balancer.

For requests to reach the Krill Manager servers, configure the load balancer
to proxy ports 80, 443 (RRDP) and 873 (Rsync).

What changes in cluster mode?
-----------------------------

Under the hood Krill Manager will use Docker Swarm to place Docker containers on
available cluster servers, with some services such as NGINX and RsyncD getting a
container on *every* server.

Krill Manager will replicate configuration and state across the cluster using a
Gluster replicated volume. Loss of a cluster server has no impact on the
configuration and data available to the other servers.

If the master server is lost the cluster continues to operate, you just can't
replace lost containers while the master is down.

Krill Manager forwards log streams to the master for uploading/processing to/by
an external entity. Krill Manager does **NOT** aggregate Prometheus metric
endpoints for you, the expectation is that the customers own Prometheus and
Grafana services will be used to do metric aggegration.

What stays the same in cluster mode?
------------------------------------

The commands used to manage Krill Manager are unchanged. They should be issued
from the master server. Krill Manager will handle upgrading all cluster servers
when an upgrade is requested.

Clients can still access the Krill UI and API, RRDP and Rsync endpoints at the
same URLs as in non-cluster mode. Requests for Krill that arrive at a different
server than the one running Krill will be proxied by the local NGINX to
whichever server is running Krill.

========
Advanced
========

Load balancers, TLS termination and real client IPs
---------------------------------------------------

A load balancer in front of Krill Manager servers can be used to terminate TLS. If
it then speaks only to private IP addresses on each Krill Manager cluster server
you may decide there is no need for real TLS certificates on the Krill Manager
NGINX RRDP servers. Passing ``--private`` to ``krillmanager init`` will cause it
to generate and use self-signed certificates for NGINX RRDP to support this use
case.

Instead, if you still wish Krill Manager servers to have real TLS certificates,
Krill Manager will take care of ensuring that the Let's Encrypt HTTP-01 challenge
is properly answered during ``krillmanager init`` even with multiple servers in
the cluster, of distributing the TLS certificates across all NGINX servers, and
will handle certificate renewal on one server and if renewed will distribute the
new certificate across all servers and signal NGINX to reload without down time.

.. note::
   By using a load balancer you will lose the real client IP addresses and so
   will not see them in your logs. One solution to this problem is to enable
   `Proxy Protocol <https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt>`_
   on your load balancer but Krill Manager does not yet support this. See: 
   `issue #2 <https://github.com/NLnetLabs/krillmanager/issues/2>`_.

How is the cluster established?
-------------------------------

1. The master server activates Docker Swarm mode becoming a Swarm Manager.
2. The master server adds the other servers as Gluster peers.
3. The master server creates a Gluster replication volume across the peers. Each
   peer will have a complete copy of the data written to the volume.
4. The master server writes the Docker Swarm join token to the Gluster volume.
5. The slave servers detect the join token and use it to join the Docker Swarm.

Can I add or remove cluster servers later?
------------------------------------------

In theory yes, but there is no support for doing so in Krill Manager. Krill
Manager will handle the loss of a cluster server but that server will still be
part of the cluster, as long as the load balancer uses a health check to avoid
sending requests to a dead server the cluster will continue to work as expected.
You would need to issue the appropriate Gluster and Docker Swarm commands to
expand or contract the size of the cluster.

Is the Swarm Manager highly available?
--------------------------------------

No. This could be done but adds complexity while adding little value. If the
manager server is lost the worst case is that the Krill UI and API become
unavailable if Krill was running on the Swarm Manager server, RRDP and Rsync
endpoints will continue to be available.

Is the Docker Swarm Routing Mesh Used?
--------------------------------------

No, the NGINX (HTTP(S)/RRDP) and Rsync containers bind directly to the host
interface ensuring that IPv6 is supported and eliminating an unnecessary
extra proxy hop.
