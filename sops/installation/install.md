# Steps for installing OCP 4.3 on bare metal:

Documentation: [docs](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.3/html/installing_on_bare_metal/installing-on-bare-metal)

## Install: 
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

```
for variant in master worker; do
cat << EOF > ./99_openshift-machineconfig_99-${variant}-nm-nodhcp.yaml

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
done
```

*   *NOTE* There is a gotcha here, fs mode is **octal** and should start with 0 eg 0644 (-rwxr--r--), however it will be **decimal** value 420 when queried later via kubernetes api.
*   Create the ignition configurations:

```
openshift-install create ignition-configs --dir=/home/dkirwan/ocp-ci-centos-org
INFO Consuming OpenShift Install (Manifests) from target directory  
INFO Consuming Common Manifests from target directory  
INFO Consuming Master Machines from target directory  
INFO Consuming Worker Machines from target directory  
INFO Consuming Openshift Manifests from target directory

# Should have the following layout
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
    *    [rhcos-4.3.8-x86_64-installer-kernel-x86_64](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer-kernel-x86_64)
    * [rhcos-4.3.8-x86_64-installer-initramfs.x86_64.img](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer-initramfs.x86_64.img)
    * [rhcos-4.3.8-x86_64-installer.x86_64.iso](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-installer.x86_64.iso)
    * [rhcos-4.3.8-x86_64-metal.x86_64.raw.gz](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/latest/rhcos-4.3.8-x86_64-metal.x86_64.raw.gz)
*   These files should be copied over to a webserver which is accessible from the bootstrap/master/compute instances.
*   **1.1.9.2.** “Configure the network boot infrastructure so that the machines boot from their local disks after RHCOS is installed on them. “
*   Existing CentOS PXE boot configuration Ansible [example](https://github.com/CentOS/ansible-infra-playbooks/blob/master/templates/pxeboot.j2)
*   Example RHCOS PXE boot configuration [here](https://projects.engineering.redhat.com/secure/attachment/104734/centos-ci-pxe_sampleconfig.txt)
*   **1.1.10. Once the systems are booting and installing, you can monitor the installation with: `./openshift-install --dir=/home/dkirwan/ocp-ci-centos-org wait-for bootstrap-complete --log-level=info`
*   Once the master nodes come up successfully, this command will exit. We can now remove the bootstrap instance, and repurpose it as a worker/compute node.
*   Run the haproxy role, once the bootstrap node has been removed from the `ocp-ci-master-and-bootstrap-stg` ansible inventory group.
*   Begin installing the compute/worker nodes.
*   Once the workers are up accept them into the cluster by accepting their `csr` certs:
```
# List the certs. If you see status pending, this is the worker/compute nodes attempting to join the cluster. It must be approved.
oc get csr

# Accept all node CSRs one liner
oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
```
*   1.1.11. Logging in to the cluster. At this point the cluster is up, and we’re in configuration territory.


## Manually test the bootstrap process RHCOS

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
