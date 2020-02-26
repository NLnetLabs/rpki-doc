.. _doc_krill_monitoring:

Monitoring
==========

The HTTPS server in Krill provides endpoints for monitoring the application. A
data format specifically for `Prometheus <https://prometheus.io/>`_ is available
and `dedicated port 9657
<https://github.com/prometheus/prometheus/wiki/Default-port-allocations>`_ has
been reserved.

On the ``/metrics`` path, Krill will expose several data points:

- A timestamp when the daemon was started
- The number of CAs Krill has configured
- The number of children for each CA
- The number of ROAs for each CA
- Timestamps when publishers were last updated
- The number of objects in the repository for each publisher
- The size of the repository, in bytes
- The RRDP serial number

This is an example of the output of the ``/metrics`` endpoint:

.. code-block:: text

  # HELP krill_server_start timestamp of last krill server start
  # TYPE krill_server_start gauge
  krill_server_start 1582189609

  # HELP krill_repo_publisher number of publishers in repository
  # TYPE krill_repo_publisher gauge
  krill_repo_publisher 1

  # HELP krill_repo_rrdp_last_update timestamp of last update by any publisher
  # TYPE krill_repo_rrdp_last_update gauge
  krill_repo_rrdp_last_update 1582700400

  # HELP krill_repo_rrdp_serial RRDP serial
  # TYPE krill_repo_rrdp_serial counter
  krill_repo_rrdp_serial 128

  # HELP krill_repo_objects number of objects in repository for publisher
  # TYPE krill_repo_objects gauge
  krill_repo_objects{publisher="acme-corp-intl"} 6

  # HELP krill_repo_size size of objects in bytes in repository for publisher
  # TYPE krill_repo_size gauge
  krill_repo_size{publisher="acme-corp-intl"} 16468

  # HELP krill_repo_last_update timestamp of last update for publisher
  # TYPE krill_repo_last_update gauge
  krill_repo_last_update{publisher="acme-corp-intl"} 1582700400

  # HELP krill_cas number of cas in krill
  # TYPE krill_cas gauge
  krill_cas 1

  # HELP krill_cas_roas number of roas for CA
  # TYPE krill_cas_roas gauge
  krill_cas_roas{ca="acme-corp-intl"} 4

  # HELP krill_cas_children number of children for CA
  # TYPE krill_cas_children gauge
  krill_cas_children{ca="acme-corp-intl"} 0

The monitoring service has several additional endpoints on the following
paths:

  :/stats/info:
       Returns the Krill version and timestamp when the daemon was
       started in a concise format

  :/stats/cas:
       Returns the number of ROAs and children each CA has

  :/stats/repo:
      Returns details on the internal repository, if configured
