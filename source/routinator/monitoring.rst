.. _doc_routinator_monitoring:

Monitoring
==========

When run in ``rtrd`` mode, Routinator can provide an HTTP service in
addition to the RTR service. The primary goal of this service is
to allow integration into monitoring systems, such as `Prometheus <https://prometheus.io/>`_. For this reason, the service does not
support HTTPS and should only be used within the local network.

Monitoring a Routinator instance can be done using the ``listen-http``
configuration option or command line parameter. For Prometheus, `port 9556 <https://github.com/prometheus/prometheus/wiki/Default-port-allocations>`_
is allocated for this use. A Routinator instance with monitoring on this port can be launched using the following command:

.. code-block:: bash

   routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323 --listen-http 192.0.2.13:9556

On the ``/metrics`` path, Routinator will expose the number of valid ROAs seen for each trust anchor, as well as the total number of validated ROA payloads (VRPs) for each:

.. code-block:: text

   # HELP valid_roas number of valid ROAs seen
   # TYPE valid_roas gauge
   valid_roas{tal="afrinic"} 181
   valid_roas{tal="apnic"} 2590
   valid_roas{tal="arin"} 4518
   valid_roas{tal="lacnic"} 2182
   valid_roas{tal="ripe"} 8796

   # HELP vrps_total total number of VRPs seen
   # TYPE vrps_total gauge
   vrps_total{tal="afrinic"} 231
   vrps_total{tal="apnic"} 18054
   vrps_total{tal="arin"} 5816
   vrps_total{tal="lacnic"} 6320
   vrps_total{tal="ripe"} 49212

The service supports several other GET requests, with the following paths:

:/csv:
     Returns the current set of VRPs in csv output format

:/json:
     Returns the current set of VRPs in json output format

:/openbgpd:
     Returns the current set of VRPs in openbgpd output format

:/rpsl:
     Returns the current set of VRPs in rpsl output format

:/version:
     Returns the version of the Routinator instance