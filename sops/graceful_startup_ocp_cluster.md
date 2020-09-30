# Graceful Startup of an Openshift 4 Cluster
This SOP should be followed in the following scenarios:

- Starting up an Openshift 4 cluster. 


## Steps

Prequisite steps:


1. Start the physical nodes

- Production uses `adhoc-openshift-nfs-stats.yaml` playbook to stop/start/restart nodes
- Staging uses seamicro accessible from admin machine, user manual contained in centosci/ocp4-docs/sops/seamicro

2. Once the nodes have been started they must be uncordoned if appropriate

```
oc get nodes
NAME                       STATUS                     ROLES    AGE    VERSION
dumpty-n1.ci.centos.org    Ready,SchedulingDisabled   worker   77d    v1.18.3+6c42de8
dumpty-n2.ci.centos.org    Ready,SchedulingDisabled   worker   77d    v1.18.3+6c42de8
dumpty-n3.ci.centos.org    Ready,SchedulingDisabled   worker   77d    v1.18.3+6c42de8
dumpty-n4.ci.centos.org    Ready,SchedulingDisabled   worker   77d    v1.18.3+6c42de8
dumpty-n5.ci.centos.org    Ready,SchedulingDisabled   worker   77d    v1.18.3+6c42de8
kempty-n10.ci.centos.org   Ready,SchedulingDisabled   worker   106d   v1.18.3+6c42de8
kempty-n11.ci.centos.org   Ready,SchedulingDisabled   worker   106d   v1.18.3+6c42de8
kempty-n12.ci.centos.org   Ready,SchedulingDisabled   worker   106d   v1.18.3+6c42de8
kempty-n6.ci.centos.org    Ready,SchedulingDisabled   master   106d   v1.18.3+6c42de8
kempty-n7.ci.centos.org    Ready,SchedulingDisabled   master   106d   v1.18.3+6c42de8
kempty-n8.ci.centos.org    Ready,SchedulingDisabled   master   106d   v1.18.3+6c42de8
kempty-n9.ci.centos.org    Ready,SchedulingDisabled   worker   106d   v1.18.3+6c42de8

nodes=$(oc get nodes -o name  | sed -E "s/node\///")

for node in ${nodes[@]}; do oc adm uncordon $node; done
node/dumpty-n1.ci.centos.org uncordoned
node/dumpty-n2.ci.centos.org uncordoned
node/dumpty-n3.ci.centos.org uncordoned
node/dumpty-n4.ci.centos.org uncordoned
node/dumpty-n5.ci.centos.org uncordoned
node/kempty-n10.ci.centos.org uncordoned
node/kempty-n11.ci.centos.org uncordoned
node/kempty-n12.ci.centos.org uncordoned
node/kempty-n6.ci.centos.org uncordoned
node/kempty-n7.ci.centos.org uncordoned
node/kempty-n8.ci.centos.org uncordoned
node/kempty-n9.ci.centos.org uncordoned

oc get nodes
NAME                       STATUS   ROLES    AGE    VERSION
dumpty-n1.ci.centos.org    Ready    worker   77d    v1.18.3+6c42de8
dumpty-n2.ci.centos.org    Ready    worker   77d    v1.18.3+6c42de8
dumpty-n3.ci.centos.org    Ready    worker   77d    v1.18.3+6c42de8
dumpty-n4.ci.centos.org    Ready    worker   77d    v1.18.3+6c42de8
dumpty-n5.ci.centos.org    Ready    worker   77d    v1.18.3+6c42de8
kempty-n10.ci.centos.org   Ready    worker   106d   v1.18.3+6c42de8
kempty-n11.ci.centos.org   Ready    worker   106d   v1.18.3+6c42de8
kempty-n12.ci.centos.org   Ready    worker   106d   v1.18.3+6c42de8
kempty-n6.ci.centos.org    Ready    master   106d   v1.18.3+6c42de8
kempty-n7.ci.centos.org    Ready    master   106d   v1.18.3+6c42de8
kempty-n8.ci.centos.org    Ready    master   106d   v1.18.3+6c42de8
kempty-n9.ci.centos.org    Ready    worker   106d   v1.18.3+6c42de8
```


### Resources

- [1] [Graceful Cluster Startup](https://docs.openshift.com/container-platform/4.5/backup_and_restore/graceful-cluster-restart.html)
- [2] [Cluster disaster recovery](https://docs.openshift.com/container-platform/4.5/backup_and_restore/disaster_recovery/scenario-2-restoring-cluster-state.html#dr-restoring-cluster-state)
