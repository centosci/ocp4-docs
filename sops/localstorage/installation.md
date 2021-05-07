# Adding Local Storage
Planning to make use of the Local Storage Operator to format the /dev/sdb disks on each node. Following the instructions at [4].

Resources:

*   [1] 1.3.12.1. [https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html/installing_on_bare_metal/installing-on-bare-metal](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html/installing_on_bare_metal/installing-on-bare-metal)
*   [2] Parameters to configure the image registry operator [https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html-single/registry/index#registry-operator-configuration-resource-overview_configuring-registry-operator](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html-single/registry/index#registry-operator-configuration-resource-overview_configuring-registry-operator)
*   [3] [https://docs.openshift.com/container-platform/4.4/storage/understanding-persistent-storage.html](https://docs.openshift.com/container-platform/4.4/storage/understanding-persistent-storage.html)
*   [4] Configuring local storage [https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-local.html](https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-local.html)
*   [5] Configuring nfs storage [https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html](https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html)
*   [6] Persistent storage accessModes [https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)

Steps:

*   Installed the Local Storage Operator via instructions at [4]
*   Created a LocalVolume object via instructions at [4], see contents: [link](https://gist.github.com/davidkirwan/4cfbee653ecbab70484c9ce878e5eb90)
*   The documentation at [4] suggest that you can simply patch the daemonset config to add configuration to run on master nodes also. This is not true. The Local Storage Operator will revert any changes to the objects which it is managing. This change instead must be made to the LocalStorage object created at step 2.
*   Daemonset pod runs on each node that matches the selector in the LocalVolume object:


```
oc get ds
NAME                        	DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
local-disks-local-diskmaker 	7     	7     	7   	7        	7       	<none>      	58m
local-disks-local-provisioner   7     	7     	7   	7        	7       	<none>      	58m
```


*   I had to manually go onto each node, and wipe the partition table on the /dev/sdb drives, then reboot the node one at a time.
*   Upon rebooting, the daemonset pods format the disks and create a persistent volume.


```
oc get pv          	 
NAME            	CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS  	CLAIM   STORAGECLASS   REASON   AGE                                           	 
local-pv-5ebe93a	223Gi  	RWO        	Delete       	Available       	local-sc            	56m                                           	 
local-pv-67553558   223Gi  	RWO        	Delete       	Available       	local-sc            	46m
local-pv-6aa59705   223Gi  	RWO        	Delete       	Available       	local-sc            	31s
local-pv-cae6207	223Gi  	RWO        	Delete       	Available       	local-sc            	9m6s
local-pv-f5985e6f   223Gi  	RWO        	Delete       	Available       	local-sc            	50m                                           	 
local-pv-f761542e   223Gi  	RWO        	Delete       	Available       	local-sc            	3m52s                                         	 
local-pv-f9d2a890   223Gi  	RWO        	Delete       	Available       	local-sc            	35m
```

*   RWO is ReadWriteOnce, which means you can only attach the volume to a single pod. Thats not what we want here, we want to be able to attach the volume to many pods potentially see [6].
*   Rather than editing each pv one at a time, and changing the access from ReadWriteOnce to ReadWriteMany instead run the following which should handle the task automatically:

```
for i in $(oc get pv --selector storage.openshift.com/local-volume-owner-namespace=local-storage -o custom-columns="name:.metadata.name" | tail -n +$((2))); do oc patch pv $i --patch '{"spec":{"accessModes":["ReadWriteMany"]}}';done
```


```
oc get pv
NAME            	CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS  	CLAIM   STORAGECLASS   REASON   AGE
local-pv-5ebe93a	223Gi  	RWX        	Delete       	Available       	local-sc            	69m
local-pv-67553558   223Gi  	RWX        	Delete       	Available       	local-sc            	60m
local-pv-6aa59705   223Gi  	RWX        	Delete       	Available       	local-sc            	14m
local-pv-cae6207	223Gi  	RWX        	Delete       	Available       	local-sc            	22m
local-pv-f5985e6f   223Gi  	RWX        	Delete       	Available       	local-sc            	64m
local-pv-f761542e   223Gi  	RWX        	Delete       	Available       	local-sc            	17m
local-pv-f9d2a890   223Gi  	RWX        	Delete       	Available       	local-sc            	49m
```
