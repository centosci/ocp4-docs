# Persistent storage via NFS
Once the NFS storage is configured and available for use within the cluster, we can create PVs with the following adhoc playbook: [ansible-infra-playbooks/adhoc-openshift-pv.yml](https://github.com/CentOS/ansible-infra-playbooks/blob/master/adhoc-openshift-pv.yml)

Sample usage:

```
ansible-playbook playbooks/adhoc-openshift-pv.yml -e "host=<admin host where the NFS storage is mounted>" -e "pv_size=10Gi" -e "cico_project_name=project-pv-name" 
```



Resources:
*   [1] Jira [https://projects.engineering.redhat.com/browse/CPE-701](https://projects.engineering.redhat.com/browse/CPE-701)
*   [2] Configuring NFS [https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html](https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html)
