# Persistent storage via NFS

```
14:29 <Arrfab> Saffronique: you have storage normally :
14:29 <Arrfab> showmount -e nfs02.ci.centos.org|grep ocp
14:30 <Arrfab>  /exports/ocp-prod  172.19.0.140,172.19.0.139,172.19.0.138,172.19.0.137,172.19.0.136,172.19.0.135,172.19.0.134

You can so use the following nfs path : nfs02.ci.centos.org:/exports/ocp-prod
```

Resources:
*   [1] Jira [https://projects.engineering.redhat.com/browse/CPE-701](https://projects.engineering.redhat.com/browse/CPE-701)
*   [2] Configuring NFS [https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html](https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html)
