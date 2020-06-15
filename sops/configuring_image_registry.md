## Image Registry

### Resources
- [1] https://docs.openshift.com/container-platform/4.4/registry/configuring_registry_storage/configuring-registry-storage-baremetal.html


### Prerequisites

- Cluster administrator permissions.
- A cluster on bare metal.
- Provision persistent storage for your cluster, such as Red Hat OpenShift Container Storage. To deploy a private image registry, your storage must provide ReadWriteMany access mode.
- Must have "100Gi" capacity.



To start the image registry, you must change ManagementState Image Registry Operator configuration from Removed to Managed.
Leave the claim field blank to allow the automatic creation of an image-registry-storage PVC.


```
$ oc edit configs.imageregistry/cluster
apiVersion: imageregistry.operator.openshift.io/v1
kind: Config
metadata:
...
spec:
...
  managementState: Managed
  storage:
    pvc:
      claim:
...
```


We want to enable the image pruner, to occationally prune images in the registry.

```
$ oc edit imagepruner.imageregistry/cluster
apiVersion: imageregistry.operator.openshift.io/v1
kind: ImagePruner
metadata:
  name: cluster
spec:
  suspend: false
...
```


Check the status of the deployment:

```
oc get clusteroperator image-registry
```

