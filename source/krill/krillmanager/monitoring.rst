.. _doc_krill_manager_monitoring:

Monitoring
==========

The available `Prometheus <https://prometheus.io/docs/concepts/data_model/>`_
endpoints for monitoring Krill Manager components can be determined using the
``krillmanager status`` command:

.. code-block:: text

   # krillmanager status
   ...
   ...
   ...
     - Prometheus monitoring endponts:
       - Krill         : http://<YOUR DOMAIN>:9657/metrics
       - NGINX         : http://<YOUR DOMAIN>:9113/metrics 
       - Docker        : http://<YOUR DOMAIN>:9323/metrics 
       - O/S           : http://<YOUR DOMAIN>:9100/metrics 
       - Gluster       : http://<YOUR DOMAIN>:8080/metrics 

.. note:: In cluster mode the per-node metrics (NGINX, Docker, O/S and Gluster)
          should be queried on the node you are interested in, Krill Manager
          does NOT aggregate cluster metrics for you.

.. tip:: Krill metrics can be queried on any cluster node, NGINX will fetch
         them from Krill on whichever cluster node the single Krill instance
         is running.

Visualisation
-------------

To visualise the monitoring endpoint metrics deploy your own Prometheus and
Grafana servers, e.g. using these DigitalOcean Marketplace Apps:

- https://marketplace.digitalocean.com/apps/prometheus
- https://marketplace.digitalocean.com/apps/grafana

Add stanzas like the following to the ``scrape_configs`` section of the
``prometheus.yml`` file on the Prometheus server and restart Prometheus:

.. code-block:: yaml

   scrape_configs:
     ...
     ...
     ...
     - job_name: 'krill'
       static_configs:
       - targets: ['<YOUR DOMAIN>:9657']

     - job_name: 'nginx'
       static_configs:
       - targets: ['<YOUR DOMAIN>:9113']

Add ``http://<PROMETHEUS DOMAIN OR IP>:9090`` as a datasource to Grafana.

Then import `Grafana Dashboards <https://grafana.com/grafana/dashboards>`_ by ID, e.g.:
  - https://grafana.com/grafana/dashboards/1860 (Node Exporter Full)
  - https://grafana.com/grafana/dashboards/8951 (Docker Monitoring)
  - https://grafana.com/grafana/dashboards/11199 (NGINX by nginxinc)

Alerting
--------

Grafana can be configured to send `notifications <https://grafana.com/docs/grafana/latest/alerting/notifications/>`_
to a variety of destination types when alert conditions are met.