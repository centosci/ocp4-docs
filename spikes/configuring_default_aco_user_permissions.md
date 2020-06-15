## Configure default permission for ACO users
By default, all users which are authenticated with Openshift (system:authenticated) will be apart of the group `self-provisioners`. This role provides the basic access to create projects etc, where the user then has admin access within.

To prevent this, we must first delete this `self-provisioner` ClusterRoleBinding. Should we ever wish to restore for whatever reason, see the following which is the original contents of the object:

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: self-provisioners
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: 'true'
subjects:
  - kind: Group
    apiGroup: rbac.authorization.k8s.io
    name: 'system:authenticated:oauth'
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: self-provisioner
```

Once removed, a new user which authenticates via ACO, now no longer has permission to do much of anything, beyond what a `basic-user` role provides.

To find this role originally, see resources [1][2]. To list the cluster roles and their bindings do the following `oc describe clusterrole.rbac` and `oc describe clusterrolebinding.rbac`. Searching for `system:authenticated` pointed toward which role was being automatically applied to the users which were authenticated with the cluster.

### Adding permissions to an authenticated user
We first create a group which will contain all the users for a particular proejct. eg:

```
kind: Group
apiVersion: user.openshift.io/v1
metadata:
  name: project-group-admins
users:
  - user2
  - user1
```

Then create a project/namespace for the project. eg: `oc create namespace "project"`

Next create a rolebinding for the group to a role. We want to give members of this group, admin access within the namespace. eg:

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: project-admins
  namespace: project
subjects:
  - kind: Group
    apiGroup: rbac.authorization.k8s.io
    name: project-group-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: admin
```

Users listed in the group will now have admin access to the project/namespace and nothing else within the cluster, which is what we want.


### Resources
- [1] Using RBAC to define and apply permissions https://docs.openshift.com/container-platform/4.4/authentication/using-rbac.html#default-roles_using-rbac
- [2] Using OIDC to authenticate https://docs.openshift.com/container-platform/4.4/authentication/identity_providers/configuring-oidc-identity-provider.html#configuring-oidc-identity-provider

