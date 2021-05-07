# Binding a PVC to a local storage PV
In order to bind a PVC to a local storage PV you can do so using the following:

Steps:

*   Create a PersistantVolumeClaim object like the following, simply update the `NAMESPACE` to match the namespace this PVC will be created in, the `NAME` the name of the PVC, the `SIZE` the size which matches the local storage PV and finally the `LOCAL_STORAGE_PV_NAME` to match the local storage PV's name which you wish to bind it to.
*   Important, don't chose a local storage PV name which exists on a master node, as they are marked as unschedulable for user workloads.


```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: NAME
  namespace: NAMESPACE
  finalizers:
    - kubernetes.io/pvc-protection
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: SIZE
  volumeName: LOCAL_STORAGE_PV_NAME
  storageClassName: local-sc
  volumeMode: Filesystem
```
