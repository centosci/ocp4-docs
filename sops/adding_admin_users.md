# Adding users to the cluster admin group
To add cluster admin privileges to a particular user do the following.

When authenticating to the Openshift cluster via ACO, it will automatically create a User object within Openshift. eg:

```
kind: User
apiVersion: user.openshift.io/v1
metadata:
  name: email@address.com
...
```

Created a Group ocp-ci-admins, and added the following users. Each "user" corresponds with the metadata, name for the corresponding User object.

```
kind: Group
apiVersion: user.openshift.io/v1
metadata:
  name: ocp-ci-admins
  selfLink: /apis/user.openshift.io/v1/groups/ocp-ci-admins
  uid: 24a5ad4d-7ee0-4e30-8f92-4b398ba5d389
  resourceVersion: '6800501'
  creationTimestamp: '2020-05-27T16:03:26Z'
users:
  - email@address.com
```

Added a ClusterRoleBinding, to bind our Group ocp-ci-admins to the ClusterRole cluster-admin

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: ocp-ci-cluster-admins
  selfLink: /apis/rbac.authorization.k8s.io/v1/clusterrolebindings/ocp-ci-cluster-admins
  uid: 7979a53b-6597-4ec7-9d6c-53b5ab8004c7
  resourceVersion: '6799178'
  creationTimestamp: '2020-05-27T16:03:58Z'
subjects:
  - kind: Group
    apiGroup: rbac.authorization.k8s.io
    name: ocp-ci-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
```

