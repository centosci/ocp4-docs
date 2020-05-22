# Lets Encrypt Certs
Need a valid cert for both the wildcard *.apps.ocp.ci.centos.org and api.ocp.ci.centos.org

Resources:
*   [1] Certman Operator (looks like this requires HIVE[5] so not going to work) [https://github.com/openshift/certman-operator](https://github.com/openshift/certman-operator)
*   [2] Changing the cert in OCP4 [https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html](https://docs.openshift.com/container-platform/4.4/authentication/certificates/replacing-default-ingress-certificate.html)
*   [3] RHMI SOP for manually replacing certs on 3.11 cluster, many steps similar: [https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc](https://github.com/RHCloudServices/integreatly-help/blob/master/sops/POC_cert_renewal.asciidoc)
*   [4] Option Brian suggested:  [https://github.com/tnozicka/openshift-acme](https://github.com/tnozicka/openshift-acme)
*   [5] HIVE [https://github.com/openshift/hive](https://github.com/openshift/hive)
