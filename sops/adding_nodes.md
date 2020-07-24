# Adding Compute/Worker nodes
This SOP should be used in the following scenario:

- Red Hat OpenShift Container Platform 4.x cluster has been installed some time ago (1+ days ago) and additional worker nodes are required to increase the capacity for the cluster.


## Steps

1. Add the new nodes being added to the cluster to the appropriate inventory file in the appropriate group.

eg:

```
# ocp, compute/worker:
[ocp-ci-compute]
newnode1.example.centos.org
newnode2.example.centos.org
newnode3.example.centos.org
newnode4.example.centos.org
newnode5.example.centos.org
```

eg:

```
# ocp.stg, compute/worker:
[ocp-stg-ci-compute]
newnode6.example.centos.org
newnode7.example.centos.org
newnode8.example.centos.org
newnode9.example.centos.org

# ocp.stg, master/control plane
[ocp-stg-ci-master]
newnode10.example.centos.org
```


2. Examine the `inventory` file for `ocp` or `ocp.stg` and determine which management node corresponds with the group `ocp-ci-management`.

eg:

```
[ocp-ci-management]
some-managementnode.example.centos.org
```

3. Find the OCP admin user which is contained in the hostvars for this management node at the key `ocp_service_account`.

eg:

```
host_vars/some-managementnode.example.centos.org:ocp_service_account: adminuser
```

4. SSH to the node identified in step `2`, and become the user identified in step `3`.

eg:

```
ssh some-managementnode.example.centos.org

sudo su - adminuser
```
  
5. Verify that you are authenticated correctly to the Openshift cluster as the `system:admin`.

```
oc whoami
system:admin
```

6. Retrieve the certificate from the internal API and convert the contents to base64 string like so.

eg:

```
echo "q" | openssl s_client -connect api-int.ocp.ci.centos.org:22623  -showcerts | awk '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/' | base64 --wrap=0
DONE
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXCERTSTOREDASABASE64ENCODEDSTRING=
```

7. Replace the cert in the compute/worker ignition file, at the `XXXXXXXXREPLACEMEXXXXXXXX=` point, be sure to save this change in SCM, and push.

```
cat filestore/rhcos/compute.ign
{"ignition":{"config":{"append":[{"source":"https://api-int.ocp.ci.centos.org:22623/config/worker","verification":{}}]},"security":{"tls":{"certificateAuthorities":[{"source":"data:text/plain;charset=utf-8;base64,XXXXXXXXREPLACEMEXXXXXXXX=","verification":{}}]}},"timeouts":{},"version":"2.2.0"},"networkd":{},"passwd":{},"storage":{"disks":[{"device":"/dev/sdb","wipeTable":true}]},"systemd":{}}
```

8. Once the ignition file has been updated, run the `adhoc-provision-ocp4-node` playbook to copy the updated ignition files up to the http server, and install the new node(s). When prompted, specify the hostname of the new node. Best to do one at a time, it takes a minute or two per new node being added at this step.

eg:

```
ansible-playbook playbooks/adhoc-provision-ocp4-node.yml
[WARNING] Nodes to be fully wiped/reinstalled with OCP => : newnode6.example.centos.org
```

9. As the new nodes are provisioned, they will attempt to join the cluster. They must first be accepted.

```
# List the certs. If you see status pending, this is the worker/compute nodes attempting to join the cluster. It must be approved.
oc get csr

# Accept all node CSRs one liner
oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
```


10. Finally run the playbook to update haproxy config to monitor the new nodes.

```
ansible-playbook playbooks/role-haproxy.yml --tags="config"
```


To see more information about adding new worker/compute nodes to a user provisioned infrastructure based OCP4 cluster see the detailed steps at [1],[2].


### Resources

- [1] [How to add Openshift 4 RHCOS worker nodes in UPI <24 hours](https://access.redhat.com/solutions/4246261)
- [2] [How to add Openshift 4 RHCOS worker nodes to UPI >24 hours](https://access.redhat.com/solutions/4799921)
