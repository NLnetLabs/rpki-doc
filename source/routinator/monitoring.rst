.. _doc_routinator_monitoring:

Monitoring
==========

When run in ``rtrd`` mode, Routinator can provide an HTTP service in
addition to the RTR service. The primary goal of this service is
to allow integration into monitoring systems, such as `Prometheus <https://prometheus.io/>`_. For this reason, the service does not
support HTTPS and should only be used within the local network.

Monitoring can enabled using the ``listen-http`` configuration option 
or command line parameter. For Prometheus, `port 9556 <https://github.com/prometheus/prometheus/wiki/Default-port-allocations>`_
is allocated for this use. A Routinator instance with monitoring on this port can be launched using the following command:

.. code-block:: bash

   routinator rtrd -a -l 192.0.2.13:3323 -l [2001:0DB8::13]:3323 --listen-http 192.0.2.13:9556

On the ``/metrics`` path, Routinator will expose the number of valid ROAs seen for each trust anchor, as well as the total number of validated ROA payloads (VRPs) for each. 

In addition, several counters are available that indicate when the last update was started, when it finished and what the duration was. This will allow you to tigger alerts, for example when the update duration is taking longer than your refresh interval. 

Lastly, the current RTR serial number is exposed, allowing you to check if this matches the serial number your connected router has. On Juniper routers you can verify the serial number with the command ``show validation session detail``, on Cisco routers with ``show ip bgp rpki server`` and on Nokia routers with ``show router origin-validation rpki-session detail``.

.. code-block:: text

   # HELP valid_roas number of valid ROAs seen
   # TYPE valid_roas gauge
   valid_roas{tal="afrinic"} 270
   valid_roas{tal="apnic"} 2902
   valid_roas{tal="arin"} 4769
   valid_roas{tal="lacnic"} 2323
   valid_roas{tal="ripe"} 9787
   
   # HELP vrps_total total number of VRPs seen
   # TYPE vrps_total gauge
   vrps_total{tal="afrinic"} 399
   vrps_total{tal="apnic"} 20318
   vrps_total{tal="arin"} 6220
   vrps_total{tal="lacnic"} 6564
   vrps_total{tal="ripe"} 53548
   
   # HELP last_update_start seconds since last update started
   # TYPE gauge
   last_update_start 568
   
   # HELP last_update_duration duration in seconds of last update
   # TYPE gauge
   last_update_duration 47
   
   # HELP last_update_done seconds since last update finished
   # TYPE gauge
   last_update_done 520
   
   # HELP serial current RTR serial number
   # TYPE gauge
   serial 0

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

:/status:
     Returns the health status of the application
