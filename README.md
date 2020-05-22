## CentOS CI Infrastructure - OpenShift cluster upgrade



---


This doc is intended to document the processes of upgrading the openshift cluster. It is an initiative of the CentOS CI Infrastructure team in CPE. \
End result: console.apps.ci.centos.org is pointed to OCP 4.x cluster and we have migrated all the projects under current version (3.6) to the newly deployed (4.3) cluster.


## Prerequisites


### Access confirmation



*   Access to [https://access.redhat.com/](https://access.redhat.com/), if not, follow the steps
    *   [https://mojo.redhat.com/docs/DOC-99172](https://mojo.redhat.com/docs/DOC-99172)
    *   [https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit](https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit)
*   Git repo for the installer [https://github.com/openshift/installer](https://github.com/openshift/installer)
*   OpenShift playground: [https://try.openshift.com](https://try.openshift.com/)
*   Access.redhat.com account, to download packages/pull secrets [https://cloud.redhat.com/openshift/install](https://cloud.redhat.com/openshift/install)
    *   openshift-install client: [https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux.tar.gz](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux.tar.gz)
    *   RHCOS download to create machines for your cluster to use during the installation [https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/latest/](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/latest/)
    *   Openshift Command Line tools: [https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz)
*   Official documentation for installation: 
    *   [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html)
    *   [https://docs.openshift.com/container-platform/4.3/architecture/architecture-installation.html#architecture-installation](https://docs.openshift.com/container-platform/4.3/architecture/architecture-installation.html#architecture-installation)
*   Access RH employee subscription benefits: \
[https://mojo.redhat.com/docs/DOC-99172 \
https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit](https://mojo.redhat.com/docs/DOC-99172)


### 


### Bootstrap node Identification

As per [1], the minimum number of nodes needed for an Openshift 4 cluster is 6



*   1 bootstrap node
*   3 master nodes
*   2 worker nodes.

As per [2] the minimum requirements for the bootstrap machine is:


<table>
  <tr>
   <td>
    <strong>Machine</strong>
   </td>
   <td>
    <strong>Operating System</strong>
   </td>
   <td>
    <strong>vCPU</strong>
   </td>
   <td>
    <strong>RAM</strong>
   </td>
   <td>
    <strong>Storage</strong>
   </td>
  </tr>
  <tr>
   <td>
    Bootstrap
   </td>
   <td>
    RHCOS
   </td>
   <td>
    4
   </td>
   <td>
    16 GB
   </td>
   <td>
    120 GB
   </td>
  </tr>
</table>




*   [1] Minimum number of nodes [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#machine-requirements_installing-bare-metal](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#machine-requirements_installing-bare-metal)
*   [2] Minimum bootstrap/master/worker node requirements [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#minimum-resource-requirements_installing-bare-metal](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#minimum-resource-requirements_installing-bare-metal)
*   [3] [https://ark.intel.com/content/www/us/en/ark/products/64591/intel-xeon-processor-e5-2640-15m-cache-2-50-ghz-7-20-gt-s-intel-qpi.html](https://ark.intel.com/content/www/us/en/ark/products/64591/intel-xeon-processor-e5-2640-15m-cache-2-50-ghz-7-20-gt-s-intel-qpi.html)


### Miscellaneous Prerequisites



*   Need internet access from the bootstrap/master/compute nodes so as to:
*   Access the Red Hat OpenShift Cluster Manager page to download the installation program and perform subscription management and entitlement. If the cluster has internet access and you do not disable Telemetry, that service automatically entitles your cluster. If the Telemetry service cannot entitle your cluster, you must manually entitle it on the Cluster registration page
*   Access quay.io to obtain the packages (images?) that are required to install your cluster.
*   Obtain the packages that are required to perform cluster updates.


### Steps for installing OCP 4.3 on bare metal:

Documentation: [docs](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html/installing_on_bare_metal/installing-on-bare-metal)


#### Prerequisites:
*   **1.1.3.1**. Before you install OpenShift Container Platform, you must provision two layer-4 load balancers.
*   Minimum of 6 nodes, 1 bootstrap node, 3 master, 2 compute.
*   **1.1.3**. See this section to see the **network ports** which are required to be open and accessible from each machine
*   Configure DHCP or set static IP addresses on each node. Be sure to configure it so the nodes always get the same IP address if configured via DHCP.


#### Install: 
* mkdir ocp-ci-centos-org
* cd ocp-ci-centos-org
* For installations of OpenShift Container Platform that use user-provisioned infrastructure, you must manually generate your installation configuration file.
* 1.1.7.1. for sample config see: [here](https://projects.engineering.redhat.com/secure/attachment/104626/install-config.yaml.bak)
*   get the **pullsecret** from [https://cloud.redhat.com/openshift/install/metal/user-provisioned](https://cloud.redhat.com/openshift/install/metal/user-provisioned) requires your access.redhat.com login.
*   “You must set the value of the replicas parameter to 0. This parameter controls the number of workers that the cluster creates and manages for you, which are functions that the cluster does not perform when you use user-provisioned infrastructure. You must manually deploy worker machines for the cluster to use before you finish installing OpenShift Container Platform.”
*   **1.1.8**. Once the **install-config.yaml** configuration has been added correctly, take a backup of this file for future installs or reference as the next step will consume it. Then run the following:
*   `openshift-install create manifests --dir=/home/dkirwan/ocp-ci-centos-org`

    INFO Consuming Install Config from target directory  


    WARNING Certificate 35183CE837878BAC77A802A8A00B6434857 from additionalTrustBundle is x509 v3 but not a certificate authority  


    WARNING Making control-plane schedulable by setting MastersSchedulable to true for  Scheduler cluster settings.

*   Running this command converts the **install-config.yaml** to a number of files eg:
```
    ~/ocp-ci-centos-org $ tree .

    .
    ├── manifests
    │   ├── 04-openshift-machine-config-operator.yaml
    │   ├── cluster-config.yaml
    │   ├── cluster-dns-02-config.yml
    │   ├── cluster-infrastructure-02-config.yml
    │   ├── cluster-ingress-02-config.yml
    │   ├── cluster-network-01-crd.yml
    │   ├── cluster-network-02-config.yml
    │   ├── cluster-proxy-01-config.yaml
    │   ├── cluster-scheduler-02-config.yml
    │   ├── cvo-overrides.yaml
    │   ├── etcd-ca-bundle-configmap.yaml
    │   ├── etcd-client-secret.yaml
    │   ├── etcd-host-service-endpoints.yaml
    │   ├── etcd-host-service.yaml
    │   ├── etcd-metric-client-secret.yaml
    │   ├── etcd-metric-serving-ca-configmap.yaml
    │   ├── etcd-metric-signer-secret.yaml
    │   ├── etcd-namespace.yaml
    │   ├── etcd-service.yaml
    │   ├── etcd-serving-ca-configmap.yaml
    │   ├── etcd-signer-secret.yaml
    │   ├── kube-cloud-config.yaml
    │   ├── kube-system-configmap-root-ca.yaml
    │   ├── machine-config-server-tls-secret.yaml
    │   ├── openshift-config-secret-pull-secret.yaml
    │   └── user-ca-bundle-config.yaml
    └── openshift
        ├── 99_kubeadmin-password-secret.yaml
    	├── 99_openshift-cluster-api_master-user-data-secret.yaml
      	├── 99_openshift-cluster-api_worker-user-data-secret.yaml
     	├── 99_openshift-machineconfig_99-master-ssh.yaml
    	├── 99_openshift-machineconfig_99-worker-ssh.yaml
    	└── openshift-install-manifests.yaml


    2 directories, 32 files
```

*    Edit **manifests/cluster-scheduler-02-config.yml** and set **mastersSchedulable** to false. This will prevent Pods from being scheduled on the master instances.
*   `sed -i 's/mastersSchedulable: true/mastersSchedulable: false/g' manifests/cluster-scheduler-02-config.yml`
*   Create the machineconfigs to disable dhcp on the master/worker nodes: 

    for variant in master worker; do

```
cat &lt;< EOF > ./99_openshift-machineconfig_99-${variant}-nm-nodhcp.yaml


    apiVersion: machineconfiguration.openshift.io/v1
    kind: MachineConfig
    metadata:
      labels:
    	machineconfiguration.openshift.io/role: ${variant}
      name: nm-${variant}-nodhcp
    spec:
      config:
    	ignition:
      	config: {}
      	security:
        	tls: {}
      	timeouts: {}
     	version: 2.2.0
    	networkd: {}
    	passwd: {}
    	storage:
      	files:
      	- contents:
          	Source: data:text/plain;charset=utf-8;base64,W21haW5dCm5vLWF1dG8tZGVmYXVsdD0qCg==
          	verification: {}
        	filesystem: root
        	mode: 0644
        	path: /etc/NetworkManager/conf.d/disabledhcp.conf
      osImageURL: ""
    EOF
```
*   **Becareful** with the gotcha, fs mode is **octal** and should start with 0 eg 0644 (-rwxr--r--), however it will be **decimal** value 420 when queried later via kubernetes api.
*   Create the ignition configurations:
*   **openshift-install create ignition-configs --dir=/home/dkirwan/ocp-ci-centos-org**

    INFO Consuming OpenShift Install (Manifests) from target directory  


    INFO Consuming Common Manifests from target directory  


    INFO Consuming Master Machines from target directory  


    INFO Consuming Worker Machines from target directory  


    INFO Consuming Openshift Manifests from target directory

```
    .
    ├── auth
    │   ├── kubeadmin-password
    │   └── kubeconfig
    ├── bootstrap.ign
    ├── master.ign
    ├── metadata.json
    └── worker.ign
```

*   **1.1.9. Creating Red Hat Enterprise Linux CoreOS (RHCOS) machines**
*   Prerequisites: 
*   Obtain the Ignition config files for your cluster. 
*   Configure suitable PXE or iPXE infrastructure. 
*   Have access to an HTTP server that you can access from your computer.
*   Have a load balancer eg Haproxy available
*   You must download the kernel, initramfs, ISO file and the RAW disk files eg:
*   [https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/)

    [rhcos-4.3.8-x86_64-installer-kernel-x86_64](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer-kernel-x86_64)


    [rhcos-4.3.8-x86_64-installer-initramfs.x86_64.img](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer-initramfs.x86_64.img)


    [rhcos-4.3.8-x86_64-installer.x86_64.iso](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer.x86_64.iso)


    [rhcos-4.3.8-x86_64-metal.x86_64.raw.gz](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-metal.x86_64.raw.gz)

*   These files should be copied over to a webserver which is accessible from the bootstrap/master/compute instances.
*   **1.1.9.2.** “Configure the network boot infrastructure so that the machines boot from their local disks after RHCOS is installed on them. “
*   Existing CentOS PXE boot configuration Ansible [example](https://github.com/CentOS/ansible-infra-playbooks/blob/master/templates/pxeboot.j2)
*   Example RHCOS PXE boot configuration [here](https://projects.engineering.redhat.com/secure/attachment/104734/centos-ci-pxe_sampleconfig.txt)
*   **1.1.10. Once the systems are booting and installing, you can monitor the installation with: `./openshift-install --dir=/home/dkirwan/ocp-ci-centos-org wait-for bootstrap-complete --log-level=info`
*   1.1.11. Logging in to the cluster. At this point the cluster is up and we’re in configuration territory.


### Manually test the bootstrap process RHCOS

Resources:



*   [1] JIRA corresponding with this section: [CPE-661](https://projects.engineering.redhat.com/browse/CPE-661)
*   [2] [https://github.com/CentOS/ansible-infra-playbooks/pull/4](https://github.com/CentOS/ansible-infra-playbooks/pull/4)
*   [3] [https://scm.infra.centos.org/CentOS/ansible-inventory-ci/pulls/1](https://scm.infra.centos.org/CentOS/ansible-inventory-ci/pulls/1)
*   [4] [https://scm.infra.centos.org/CentOS/ansible-pkistore-ci/pulls/1](https://scm.infra.centos.org/CentOS/ansible-pkistore-ci/pulls/1)
*   [5] [CentOS/ansible-infra-playbooks/staging/templates/ocp_pxeboot.j2](https://raw.githubusercontent.com/CentOS/ansible-infra-playbooks/staging/templates/ocp_pxeboot.j2)
*   [https://www.openshift.com/blog/openshift-4-bare-metal-install-quickstart](https://www.openshift.com/blog/openshift-4-bare-metal-install-quickstart)
*   [6] [Create a raid enabled data volume via ignition file](https://coreos.com/ignition/docs/latest/examples.html#create-a-raid-enabled-data-volume)
*   [7] HAProxy config for OCP4 [https://github.com/openshift-tigerteam/guides/blob/master/ocp4/ocp4-haproxy.cfg](https://github.com/openshift-tigerteam/guides/blob/master/ocp4/ocp4-haproxy.cfg)

Steps:



*   Create ssh key pair using `ssh-keygen` and uploaded it to the ansible-pkistore-ci repository at [4]
*   Through trial and error, we’ve produced a PXE boot configuration for one of the machines and managed to get it to boot and begin the bootstrap process via an ignition file see [5].
*   Next steps is to make a decision on networking configuration then configure DNS and create 2 haproxy proxies before creating the bootstrap and master OCP nodes. Jiras created: [CPE-678](https://projects.engineering.redhat.com/browse/CPE-678), [CPE-677](https://projects.engineering.redhat.com/browse/CPE-677) and [CPE-676](https://projects.engineering.redhat.com/browse/CPE-676)
*   PR configuration for the HAProxy loadbalancers: [here](https://github.com/CentOS/ansible-role-haproxy/pull/2)
*   Configuration for DNS/bind (encrypted): [here](https://scm.infra.centos.org/CentOS/ansible-filestore-ci/src/branch/master/bind/ci.centos.org)



**Engineering queries**



*   Ansible role discussion
    *   Supportive info: [https://github.com/CentOS/ansible-role-keepalived/blob/master/tasks/main.yml#L4](https://github.com/CentOS/ansible-role-keepalived/blob/master/tasks/main.yml#L4)
*   Load balancer 
    *   Supportive info:[https://github.com/CentOS/ansible-role-haproxy/pull/2](https://github.com/CentOS/ansible-role-haproxy/pull/2)

```
[ocp-ci:children]
ocp-ci-bootstrap
ocp-ci-master
ocp-ci-infra
ocp-ci-compute
ocp-ci-master-and-bootstrap
 

[ocp-ci-master-and-bootstrap] #adding a **ocp-ci-master-and-bootstrap** 
n6.kempty.ci.centos.org
n7.kempty.ci.centos.org
n8.kempty.ci.centos.org
n12.kempty.ci.centos.org
 

[ocp-ci-bootstrap]
n12.kempty.ci.centos.org


[ocp-ci-master]
n6.kempty.ci.centos.org
n7.kempty.ci.centos.org
n8.kempty.ci.centos.org
 

[ocp-ci-infra]
# Could maybe choose one as an infra node for hosting internal things vOv


[ocp-ci-compute]
n9.kempty.ci.centos.org
n10.kempty.ci.centos.org
n11.kempty.ci.centos.org
# n12.kempty.ci.centos.org
```

Pushed this change: https://scm.infra.centos.org/CentOS/ansible-inventory-ci/pulls/4

[https://github.com/CentOS/ansible-infra-playbooks/blob/staging/templates/ocp_pxeboot.j2](https://github.com/CentOS/ansible-infra-playbooks/blob/staging/templates/ocp_pxeboot.j2)

Cp-ci-master-and-bootstrap

Openshift team blog post: 

[https://www.openshift.com/blog/openshift-4-bare-metal-install-quickstart](https://www.openshift.com/blog/openshift-4-bare-metal-install-quickstart)

[https://github.com/openshift-tigerteam/guides/blob/master/ocp4/ocp4-haproxy.cfg](https://github.com/openshift-tigerteam/guides/blob/master/ocp4/ocp4-haproxy.cfg)

  ```
root@humpty-n1 ~]# cat /proc/mdstat
Personalities : [raid1]  
md126 : active raid1 sda2[0] sdb2[1]
      194179072 blocks super 1.2 [2/2] [UU]
      bitmap: 2/2 pages [8KB], 65536KB chunk
 
md127 : active raid1 sda1[0] sdb1[1]
      1046528 blocks super 1.2 [2/2] [UU]
      bitmap: 0/1 pages [0KB], 65536KB chunk
 
unused devices: <none>

[root@humpty-n1 ~]# mdadm --detail /dev/md126
/dev/md126:
           Version : 1.2
     Creation Time : Mon Apr 20 16:44:01 2020
        Raid Level : raid1
        Array Size : 194179072 (185.18 GiB 198.84 GB)
     Used Dev Size : 194179072 (185.18 GiB 198.84 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent
 
     Intent Bitmap : Internal
 
       Update Time : Fri May  8 15:32:08 2020
             State : clean  
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0
 
Consistency Policy : bitmap
 
              Name : humpty-n1.ci.centos.org:pv00  (local to host humpty-n1.ci.centos.org)
              UUID : 552cae55:f7651069:8a22b42d:85e8ef71
            Events : 582
 
    Number   Major   Minor   RaidDevice State
       0       8        2        0      active sync   /dev/sda2
       1       8       18        1      active sync   /dev/sdb2
[Prod - humpty-n1.ci.centos.org]




[root@n4-136 ocp-ci-centos-org]# ls -l ~/bin                                                                         |
total 494632                                                                                                         |
-rwxr-xr-x. 2 root root  75916904 Apr 26 19:59 kubectl                                                               |
-rwxr-xr-x. 2 root root  75916904 Apr 26 19:59 oc                                                                    |
-rwxr-xr-x. 1 root root 354664448 Apr 26 20:07 openshift-install                                                     |
[root@n4-136 ocp-ci-centos-org]# kubectl version                                                                     |
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.0-4-g38212b5", GitCommit:"2576e482bf003e34e67ba|
3d69edcf5d411cfd6f3", GitTreeState:"clean", BuildDate:"2020-04-26T19:58:26Z", GoVersion:"go1.13.4", Compiler:"gc", Pl|
atform:"linux/amd64"}                                                                                                |
[root@n4-136 ocp-ci-centos-org]# oc version                                                                           
Client Version: 4.4.3                                                                                                 
[root@n4-136 ocp-ci-centos-org]# openshift-install version                                                            
openshift-install 4.4.3                                                                                              |
built from commit 78b817ceb7657f81176bbe182cc6efc73004c841
```

release image quay.io/openshift-release-dev/ocp-release@sha256:039a4ef7c128a049ccf916a1d68ce93e8f5494b44d5a75df60c85e
9e7191dacc                         	


### Adding some workloads for testing

Openshift 4, ships with a number of operators already configured and available via OperatorHub. We have tested with the Jenkinsci operator 0.4.0[1]. 

Resources:



*   [1] jenkinsci/kubernetes/operator: [github](https://github.com/jenkinsci/kubernetes-operator)
*   [2] Deploy the jenkinsci/kubernetes-operator on Kubernetes: [deploy yaml](https://raw.githubusercontent.com/jenkinsci/kubernetes-operator/master/deploy/all-in-one-v1alpha2.yaml)
*   [3] Changes required to make this work correctly on Openshift: [gist](https://gist.github.com/davidkirwan/d3301c550c94dd1a95965dd8d7a91594)


### Adding Local Storage

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

local-disks-local-diskmaker 	7     	7     	7   	7        	7       	&lt;none>      	58m

local-disks-local-provisioner   7     	7     	7   	7        	7       	&lt;none>      	58m
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
*   I edited each pv one at a time, and changed the access from ReadWriteOnce to ReadWriteMany

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

### Persistent storage via NFS
```
14:29 &lt; Arrfab> Saffronique: you have storage normally :

14:29 &lt; Arrfab> showmount -e nfs02.ci.centos.org|grep ocp

14:30 &lt; Arrfab>  /exports/ocp-prod  172.19.0.140,172.19.0.139,172.19.0.138,172.19.0.137,172.19.0.136,172.19.0.135,172.19.0.134

You can so use the following nfs path : nfs02.ci.centos.org:/exports/ocp-prod
```

Resources:
*   [1] Jira [https://projects.engineering.redhat.com/browse/CPE-701](https://projects.engineering.redhat.com/browse/CPE-701)
*   [2] Configuring NFS [https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html](https://docs.openshift.com/container-platform/4.4/storage/persistent_storage/persistent-storage-nfs.html)
 


### Openshift 4 Upgrades

Resources
*   [1] [https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html](https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html)
*   [2] Video of an upgrade 4.4.3 -> 4.4.4 [https://drive.google.com/file/d/1-ku2Xq5k0KLgcC53-KF-OA7wi9tyteBK/view](https://drive.google.com/file/d/1-ku2Xq5k0KLgcC53-KF-OA7wi9tyteBK/view)


### SPIKE: Specific workloads on Specific Nodes

JIRA: [link](https://projects.engineering.redhat.com/browse/CPE-702)

Resources:



*   [1] Advanced pod scheduling [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-about.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-about.html)
*   [2] Pod placing using node affinity rules [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-affinity.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-affinity.html)
*   [3] Pod placing using node taints, and pod tolerations [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-taints-tolerations.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-taints-tolerations.html)
*   [4] Pod affinity/anti-affinity [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-pod-affinity.html#nodes-scheduler-pod-affinity](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-pod-affinity.html#nodes-scheduler-pod-affinity)
   
*   [6] Cluster [https://github.com/storageos/cluster-operator](https://github.com/storageos/cluster-operator)


I’m thinking we should go with a mixture of two modes, taint/toleration and node affinity in order to target a specific class of compute node with privileged network access and ensure only specific workloads may be scheduled there, and so access them.

Creating a POC:

Steps:
```
oc new-project targeting-specific-nodes

oc get nodes

    NAME                   	STATUS   ROLES	AGE	VERSION


    kempty-n10.ci.centos.org   Ready	worker   7d5h   v1.17.1


    kempty-n11.ci.centos.org   Ready	worker   7d5h   v1.17.1


    kempty-n12.ci.centos.org   Ready	worker   7d5h   v1.17.1


    kempty-n6.ci.centos.org	Ready	master   7d5h   v1.17.1


    kempty-n7.ci.centos.org	Ready	master   7d5h   v1.17.1


    kempty-n8.ci.centos.org	Ready	master   7d5h   v1.17.1


    kempty-n9.ci.centos.org	Ready	worker   7d5h   v1.17.1


oc adm taint nodes kempty-n9.ci.centos.org examplekey=examplevalue:NoSchedule

oc label node kempty-n9.ci.centos.org examplekey=examplevalue

oc get node kempty-n9.ci.centos.org -o yaml

    …
      labels:
               ...
    	examplekey: examplevalue
               ...
   ...
    spec:
      taints:
      - effect: NoSchedule
    	key: examplekey
    	value: examplevalue
    …


oc apply -f targetspecificnodes.yaml

oc get pods --all-namespaces -o wide | grep kempty-n9

    ...
    targeting-specific-nodes                            	example-57d74cf6b4-mlppz                                     	1/1 	Running 	0      	13s 	10.130.2.15	kempty-n9.ci.centos.org	&lt;none>       	&lt;none>

    targeting-specific-nodes                            	example-57d74cf6b4-sgnzh                                      	1/1 	Running 	0      	13s 	10.130.2.17	kempty-n9.ci.centos.org	&lt;none>       	&lt;none>

    targeting-specific-nodes                            	example-57d74cf6b4-swllc                                      	1/1 	Running 	0      	13s 	10.130.2.16	kempty-n9.ci.centos.org	&lt;none>       	&lt;none>
    ...

```

### Lets Encrypt Certs

Need a valid cert for both the wildcard *.apps.ocp.ci.centos.org and api.ocp.ci.centos.org

Resources:



*   [1] Certman Operator (looks like this requires HIVE[5] so not going to work) [https://github.com/openshift/certman-operator](https://github.com/openshift/certman-operator)
*   [2] Changing the cert in OCP4 [https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html](https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html)
*   [3] RHMI SOP for manually replacing certs on 3.11 cluster, many steps similar: [https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc](https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc)
*   [4] Option Brian suggested:  [https://github.com/tnozicka/openshift-acme](https://github.com/tnozicka/openshift-acme)
*   [5] HIVE [https://github.com/openshift/hive](https://github.com/openshift/hive)


### Monitoring

There is a monitoring stack deployed as part of Openshift, see the “openshift-monitoring” namespace. View routes to see the Prometheus, Grafana and Alertmanager services.

We can use this monitoring stack, or alternatively deploy our own via [1]. Either way, we are responsible for maintaining it since its baremetal.

Resources:



*   [1] [https://docs.openshift.com/container-platform/4.4/monitoring/monitoring-your-own-services.html](https://docs.openshift.com/container-platform/4.4/monitoring/monitoring-your-own-services.html)
*   [2] Prometheus exporting to Zabbix [https://www.zabbix.com/integrations/prometheus](https://www.zabbix.com/integrations/prometheus)
