# Spike: Investigate adding routes from apps.ci.centos.org
The Ingress Operator[1] manages `IngressController` resources which will allow us to achieve[3] this on Openshift 4.

### Resources
- [1] https://docs.openshift.com/container-platform/4.4/networking/ingress-operator.html
- [2] https://rcarrata.com/openshift/ocp4_route_sharding/
- [3] https://projects.engineering.redhat.com/browse/CPE-764


### POC
Performed the following steps to achieve goal:

```
-rw-rw-r--. 1 dkirwan dkirwan 1060 Jul  6 18:12 deployment.yaml
-rw-rw-r--. 1 dkirwan dkirwan  286 Jul  6 17:13 ingresscontroller.yaml
-rw-rw-r--. 1 dkirwan dkirwan  336 Jul  6 17:53 route.yaml
-rw-rw-r--. 1 dkirwan dkirwan  273 Jul  6 17:58 service.yaml
```

- Created an `IngressController` which creates 2 router replicas and configured to manage Routes which point at `*.apps.ci.centos.org`. It also has a `routeSelector` to match labels `type: sharded`
```
  routeSelector:
    matchLabels:
      type: sharded
``` 
- Created a Deployment with simple app. 
- Created Service and Route to expose the app externally at `ingress-controller-test.apps.ci.centos.org`.
- Route has been given a label: `type: sharded`
- Used `dig` to retrieve the public IP address of the cluster
```
dig console-openshift-console.apps.ocp.stg.ci.centos.org

; <<>> DiG 9.11.18-RedHat-9.11.18-1.fc32 <<>> console-openshift-console.apps.ocp.stg.ci.centos.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 21722
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;console-openshift-console.apps.ocp.stg.ci.centos.org. IN A

;; ANSWER SECTION:
console-openshift-console.apps.ocp.stg.ci.centos.org. 600 IN A 8.43.84.237

;; Query time: 77 msec
;; SERVER: 10.38.5.26#53(10.38.5.26)
;; WHEN: Mon Jul 06 18:43:35 IST 2020
;; MSG SIZE  rcvd: 97
```
- Configured my `/etc/hosts` file accordingly:
```
 cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
8.43.84.237 ingress-controller-test.apps.ci.centos.org
```
- Visited `http://ingress-controller-test.apps.ci.centos.org` and was greeted with the expected content in the deployed app.
- We should be able to achieve this spike CPE-764 using the Ingress Controller Operator.



