# Adding Taints to a nodes
A taint allows a Node to control which pods should or should not be scheduled on them. A toleration is something which can be applied to a pod, to indicate that it can tolerate a taint, and may mark it as being schedulable on a node with the matching taint.

To view the official docs for Openshift/Kubernetes see [1]. This also provides information on some of the default taints which have special meaning in a Kubernetes environment.

## Example taint
The following example `node.kubernetes.io/unschedulable` is an example of a special taint which can be applied to a Node configuration. Internal Openshift/Kubernetes systems have tolerations in place by default for. With this knowledge, we can use it to prevent user workloads from being scheduled, while leaving internal system workloads in place. The effect `PreferNoSchedule` applys the following logic:

- New pods which dont have this taint will not get scheduled on a node with this taint
- Existing pods will be allowed to run

For the full list of effects see the official documentation at [1].

```
spec:
  taints:
    - key: node.kubernetes.io/unschedulable
      effect: PreferNoSchedule
```


### Resources

- [1] [Controlling Pod Placement using Node Taints](https://docs.openshift.com/container-platform/4.5/nodes/scheduling/nodes-scheduler-taints-tolerations.html)
