.. _doc_routinator_monitoring:

Monitoring
==========

The HTTP server in Routinator provides endpoints for monitoring the application. A data format specifically for `Prometheus
<https://prometheus.io/>`_ is available, as well as `dedicated port 9556
<https://github.com/prometheus/prometheus/wiki/Default-port-allocations>`_.

This means it may be a good idea to run the HTTP server alongside
the RTR server. To launch Routinator in server mode on 192.0.2.13 with RTR
running on port 3323 and HTTP on 9556, use the following command:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --http 192.0.2.13:9556

On the ``/metrics`` path, Routinator will expose the number of valid 
ROAs seen for each trust anchor, as well as the total number of validated 
ROA payloads (VRPs) for each. 

In addition, several counters are available that indicate when the last 
update was started, when it finished and what the duration was. This will 
allow you to tigger alerts, for example when the update duration is taking
longer than your refresh interval. 

Lastly, the current serial number for RPKI-RTR is exposed. This number is used
to notify connected routers that new data is available. The number Routinator
has should match the serial on your connected router. You can verify this on your router with using the following command:

:Juniper:
     ``show validation session detail``

:Cisco: 
     ``show ip bgp rpki server``

:Nokia:
     ``show router origin-validation rpki-session detail``

This is an example of the output of the ``/metrics`` endpoint:

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
   serial 42

The HTTP service has two additional endpoints on the following paths:

:/status:
     Returns the information from the ``/metrics`` endpoint in a more 
     friendly format

:/version:
     Returns the version of the Routinator instance
