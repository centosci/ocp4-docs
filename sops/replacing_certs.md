# Lets Encrypt Certs
We need a valid cert for both the wildcard \*.apps.ocp.ci.centos.org and api.ocp.ci.centos.org. We have a role/task which covers the replacing of TLS certs on ocp.ci/ocp.stg.ci `https://github.com/CentOS/ansible-role-ocp-admin-node/blob/master/tasks/tls.yml`.

A separate process is performed to request certs from LetsEncrypt, and stores the cacert/certs/key within the pkistore for ocp.ci/ocp.stg.ci.

To deploy the certs to the cluster, we run the following playbook: `https://github.com/CentOS/ansible-infra-playbooks/blob/master/role-ocp-admin-node.yml`.

eg:

```
ansible-playbook playbooks/role-ocp-admin-node.yml --tags "tls, certs"
```


Resources:
*   [1] Certman Operator (looks like this requires HIVE[5] so not going to work) [https://github.com/openshift/certman-operator](https://github.com/openshift/certman-operator)
*   [2] Changing the cert in OCP4 [https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html](https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html)
*   [3] RHMI SOP for manually replacing certs on 3.11 cluster, many steps similar: [https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc](https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc)
*   [4] Option Brian suggested:  [https://github.com/tnozicka/openshift-acme](https://github.com/tnozicka/openshift-acme)
*   [5] HIVE [https://github.com/openshift/hive](https://github.com/openshift/hive)
