.. _doc_routinator_monitoring:

Monitoring
==========

The HTTP server in Routinator provides endpoints for monitoring the application.
A data format specifically for `Prometheus <https://prometheus.io/>`_ is
available, as well as `dedicated port 9556
<https://github.com/prometheus/prometheus/wiki/Default-port-allocations>`_.

This means it may be a good idea to run the HTTP server alongside
the RTR server. To launch Routinator in server mode on 192.0.2.13 with RTR
running on port 3323 and HTTP on 9556, use the following command:

.. code-block:: bash

   routinator server --rtr 192.0.2.13:3323 --http 192.0.2.13:9556

On the ``/metrics`` path, Routinator will expose the number of valid ROAs seen
for each trust anchor, as well as the total number of validated ROA payloads
(VRPs) for each.

In addition, several counters are available that indicate when the last update
was started, when it finished and what the duration was. This will allow you to
tigger alerts, for example when the update duration is taking longer than your
refresh interval.

The current serial number for RPKI-RTR is also exposed. This number is used to
notify connected routers that new data is available. The number Routinator has
should match the serial on your connected router. You can verify this on your
router with using the following command:

:Juniper:
     ``show validation session detail``

:Cisco:
     ``show ip bgp rpki server``

:Nokia:
     ``show router origin-validation rpki-session detail``

Lastly, the endpoint provides several gauges related to rsync, such as the `exit
code <https://lxadm.com/Rsync_exit_codes>`_ and the duration of fetching the
data in each RPKI repository.

This is an example of the output of the ``/metrics`` endpoint:

.. code-block:: text

   # HELP routinator_valid_roas number of valid ROAs seen
   # TYPE routinator_valid_roas gauge
   routinator_valid_roas{tal="afrinic"} 338
   routinator_valid_roas{tal="lacnic"} 2435
   routinator_valid_roas{tal="apnic"} 3187
   routinator_valid_roas{tal="ripe"} 10779
   routinator_valid_roas{tal="arin"} 4964

   # HELP routinator_vrps_total total number of VRPs seen
   # TYPE routinator_vrps_total gauge
   routinator_vrps_total{tal="afrinic"} 459
   routinator_vrps_total{tal="lacnic"} 7042
   routinator_vrps_total{tal="apnic"} 21941
   routinator_vrps_total{tal="ripe"} 56909
   routinator_vrps_total{tal="arin"} 6621

   # HELP routinator_last_update_start seconds since last update started
   # TYPE routinator_last_update_start gauge
   routinator_last_update_start 568

   # HELP routinator_last_update_duration duration in seconds of last update
   # TYPE routinator_last_update_duration gauge
   routinator_last_update_duration 47

   # HELP routinator_last_update_done seconds since last update finished
   # TYPE routinator_last_update_done gauge
   routinator_last_update_done 520

   # HELP routinator_serial current RTR serial number
   # TYPE routinator_serial gauge
   routinator_serial 42

   # HELP routinator_rsync_status exit status of rsync command
   # TYPE routinator_rsync_status gauge
   routinator_rsync_status{uri="rsync://rpki-repository.nic.ad.jp/ap/"} 0
   routinator_rsync_status{uri="rsync://rpki.apnic.net/repository/"} 0
   routinator_rsync_status{uri="rsync://rpkica.twnic.tw/rpki/"} 0
   routinator_rsync_status{uri="rsync://ca.rg.net/rpki/"} 0
   routinator_rsync_status{uri="rsync://rpki.cnnic.cn/rpki/"} 0
   routinator_rsync_status{uri="rsync://rpki.afrinic.net/repository/"} 0
   routinator_rsync_status{uri="rsync://rpki.apnic.net/member_repository/"} 0
   routinator_rsync_status{uri="rsync://rpki.arin.net/repository/"} 0
   routinator_rsync_status{uri="rsync://repository.lacnic.net/rpki/"} 0
   routinator_rsync_status{uri="rsync://rpki.ripe.net/repository/"} 0
   routinator_rsync_status{uri="rsync://rpki.ripe.net/ta/"} 0


   # HELP routinator_rsync_duration duration of rsync in seconds
   # TYPE routinator_rsync_duration gauge
   routinator_rsync_duration{uri="rsync://rpki-repository.nic.ad.jp/ap/"} 3.050
   routinator_rsync_duration{uri="rsync://rpki.apnic.net/repository/"} 3.839
   routinator_rsync_duration{uri="rsync://rpkica.twnic.tw/rpki/"} 4.201
   routinator_rsync_duration{uri="rsync://ca.rg.net/rpki/"} 1.543
   routinator_rsync_duration{uri="rsync://rpki.cnnic.cn/rpki/"} 4.603
   routinator_rsync_duration{uri="rsync://rpki.afrinic.net/repository/"} 2.512
   routinator_rsync_duration{uri="rsync://rpki.apnic.net/member_repository/"} 6.496
   routinator_rsync_duration{uri="rsync://rpki.arin.net/repository/"} 6.516
   routinator_rsync_duration{uri="rsync://repository.lacnic.net/rpki/"} 10.506
   routinator_rsync_duration{uri="rsync://rpki.ripe.net/repository/"} 11.766
   routinator_rsync_duration{uri="rsync://rpki.ripe.net/ta/"} 0.129

The HTTP service has two additional endpoints on the following paths:

:/status:
     Returns the information from the ``/metrics`` endpoint in a more
     concise format

:/version:
     Returns the version of the Routinator instance
