# Graceful Shutdown of an Openshift 4 Cluster
This SOP should be followed in the following scenarios:

- Shutting down an Openshift 4 cluster. 


## Steps

Prequisite steps:
- Follow the SOP for cordoning and draining the nodes.
- Follow the SOP for creating an `etcd` backup.


1. Get the nodes

```
nodes=$(oc get nodes -o name  | sed -E "s/node\///")
```

2. Shutdown the nodes from the administration box associated with the cluster eg prod/staging.

```
for node in ${nodes[@]}; do ssh -i <ssh_key> core@$node sudo shutdown -h now; done
```


### Resources

- [1] [Graceful Cluster Shutdown](https://docs.openshift.com/container-platform/4.5/backup_and_restore/graceful-cluster-shutdown.html)
