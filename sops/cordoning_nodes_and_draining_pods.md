# Cordoning Nodes and Draining Pods
This SOP should be followed in the following scenarios:

- If maintenance is scheduled to be carried out on an Openshift node.


## Steps

1. Mark the node as unschedulable:

```
oc adm cordon <node1>
node/<node1> cordoned
```

2. Check that the node status is `NotReady,SchedulingDisabled`

```
oc get node <node1>
NAME        STATUS                        ROLES     AGE       VERSION
<node1>     NotReady,SchedulingDisabled   worker    1d        v1.18.3
```

Note: It might not switch to `NotReady` immediately, there maybe many pods still running.


3. Evacuate the Pods using one of the following methods
This will drain node `<node1>`, delete any local data, and ignore daemonsets, and give a period of 30 minutes for pods to drain gracefully.

```
oc adm drain <node1> --delete-local-data=true --ignore-daemonsets=true --grace-period=30
```

4. Perform the scheduled maintenance on the node
Do what ever is required in the scheduled maintenance window


5. Once the node is ready to be added back into the cluster
We must uncordon the node. This allows it to be marked scheduleable once more.

```
oc adm uncordon <node1>
```


### Resources

- [1] [Nodes - working with nodes](https://docs.openshift.com/container-platform/4.5/nodes/nodes/nodes-nodes-working.html)
