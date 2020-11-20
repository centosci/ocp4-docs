# CentOS CI Infra Outage Preparation
During a scheduled outage where it is likely we will lose network access entirely to the entire rack, or between racks, it is advisable to shutdown the following services:

- Duffy
- CentOS CI Openshift prod/stg
- Legacy CI Jenkins
- Legacy OKD
- keepalived on gateway02.ci.centos.org


## Legacy OKD
1. bstinson, as the only person on the team which has access to the legacy OKD cluster, must handle tasks related to this cluster.


## OCP
https://github.com/centosci/ocp4-docs/blob/master/sops/create_etcd_backup.md
https://github.com/centosci/ocp4-docs/blob/master/sops/cordoning_nodes_and_draining_pods.md
https://github.com/centosci/ocp4-docs/blob/master/sops/graceful_shutdown_ocp_cluster.md

Admin nodes
Prod: ocp-admin.ci.centos.org
Stg: n4-136.cloud.ci.centos.org

2. Take etcd backup to the admin node associated with prod/stg
3. Cordon and drain all nodes
4. gracefully shutdown


## Duffy

5. switch off duffy - workers
    * source duffy2-venv/bin/activate; FLASK_APP=duffy DUFFY_SETTINGS=/etc/duffy.conf python scripts/worker.py
6. switch off duffy server
    * FLASK_APP=duffy DUFFY_SETTINGS=/etc/duffy.conf flask run -h 0.0.0.0 -p 8080
7. ci.centos.org legacy jenkins: manage jenkins, prepare for shutdown
    * ssh jenkins - systemctl restart jenkins

## keepalived on Gateway nodes

8. Shutdown keepalived on gateway02.ci.centos.org
   *  sudo systemctl stop keepalived


