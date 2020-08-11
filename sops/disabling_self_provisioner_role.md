# Disabling self-provisioners role
By default, when a user authenticates with Openshift via Oauth, it is part of the `self-provisioners` group. This group provides the ability to create new projects. On CentOS CI we do not want users to be able to create their own projects, as we have a system in place where we create a project and control the administrators of that project.

To disable the self-provisioner role do the following as outlined in the documentation[1].

```
oc describe clusterrolebinding.rbac self-provisioners

Name:		self-provisioners
Labels:		<none>
Annotations:	rbac.authorization.kubernetes.io/autoupdate=true
Role:
  Kind:	ClusterRole
  Name:	self-provisioner
Subjects:
  Kind	Name				Namespace
  ----	----				---------
  Group	system:authenticated:oauth
```

Remove the subjects that the self-provisioners role applies to.

```
oc patch clusterrolebinding.rbac self-provisioners -p '{"subjects": null}'
```

Verify the change occurred successfully

```
oc describe clusterrolebinding.rbac self-provisioners
Name:         self-provisioners
Labels:       <none>
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
Role:
  Kind:  ClusterRole
  Name:  self-provisioner
Subjects:
  Kind  Name  Namespace
  ----  ----  ---------
```

When the cluster is updated to a new version, unless we mark the role appropriately, the permissions will be restored after the update is complete.

Verify that the value is currently set to be restored after an update:

```
oc get clusterrolebinding.rbac self-provisioners -o yaml
```

```
apiVersion: authorization.openshift.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  ...
```

We wish to set this `rbac.authorization.kubernetes.io/autoupdate` to `false`. To patch this do the following.

```
oc patch clusterrolebinding.rbac self-provisioners -p '{ "metadata": { "annotations": { "rbac.authorization.kubernetes.io/autoupdate": "false" } } }'
```



### Resources

- [1] https://docs.openshift.com/container-platform/4.4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation
