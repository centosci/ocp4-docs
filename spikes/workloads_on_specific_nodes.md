### SPIKE: Specific workloads on Specific Nodes
JIRA: [link](https://projects.engineering.redhat.com/browse/CPE-702)

Resources:


*   [1] Advanced pod scheduling [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-about.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-about.html)
*   [2] Pod placing using node affinity rules [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-affinity.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-affinity.html)
*   [3] Pod placing using node taints, and pod tolerations [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-taints-tolerations.html](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-taints-tolerations.html)
*   [4] Pod affinity/anti-affinity [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-pod-affinity.html#nodes-scheduler-pod-affinity](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-pod-affinity.html#nodes-scheduler-pod-affinity)
*   [5] Cluster/Project wide Node Affinity [https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors](https://docs.openshift.com/container-platform/4.4/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors)
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
    targeting-specific-nodes                            	example-57d74cf6b4-mlppz                                     	1/1 	Running 	0      	13s 	10.130.2.15	kempty-n9.ci.centos.org	<none>       	<none>

    targeting-specific-nodes                            	example-57d74cf6b4-sgnzh                                      	1/1 	Running 	0      	13s 	10.130.2.17	kempty-n9.ci.centos.org	<none>       	<none>
    targeting-specific-nodes                            	example-57d74cf6b4-swllc                                      	1/1 	Running 	0      	13s 	10.130.2.16	kempty-n9.ci.centos.org	<none>       	<none>
    ...
```
