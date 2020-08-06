# Adding Privileged SCC to Service Accounts
This SOP should be used in the following scenario:

- A tenant has been approved to run `privileged container` workloads.


## Steps

1. Add the `prvileged` security context constraint to the service account in the tenants namespace like so:

```
oc adm policy add-scc-to-user privileged -n namespace -z myserviceaccount
```



### Resources

- [1] [How to add the privileged SCC to a service account](https://docs.openshift.com/container-platform/4.5/cli_reference/openshift_cli/administrator-cli-commands.html#policy)
