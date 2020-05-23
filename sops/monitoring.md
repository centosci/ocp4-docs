# Monitoring
There is a monitoring stack deployed as part of Openshift, see the “openshift-monitoring” namespace. View routes to see the Prometheus, Grafana and Alertmanager services.
We can use this monitoring stack, or alternatively deploy our own via [1]. Either way, we are responsible for maintaining it since its baremetal.

Resources:
*   [1] [https://docs.openshift.com/container-platform/4.4/monitoring/monitoring-your-own-services.html](https://docs.openshift.com/container-platform/4.4/monitoring/monitoring-your-own-services.html)
*   [2] Prometheus exporting to Zabbix [https://www.zabbix.com/integrations/prometheus](https://www.zabbix.com/integrations/prometheus)
