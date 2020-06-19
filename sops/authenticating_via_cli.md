## Authenticating via CLI
Members of the CentOS CI Infrastructure team have admin access for ocp.ci and ocp.stg.ci Openshift clusters for their ACO accounts.

To login via the CLI using your main account authenticated via ACO simply:

- Authenticate via accounts-centos-org option in ocp.ci/ocp.stg.ci
- In the top right of the Openshift console, click the drop down menu for your user and click `Copy Login Command`
- Copy the `Log in with this token: oc login --token=xxxx --server=https://uri` and paste into your terminal

To login via the CLI using the `system:admin` user simply:

- ssh to the admin node which corresponds with `ocp-ci-management` or `ocp-ci-management-stg` inventory group
- Change user to the ocp admin user on the admin node choose appropriate version: `sudo su - ocpadm` or for staging `sudo su - ocpadmstg`
- `export /home/<ocpadmuser>/.kube/config`
- You should now have `system:admin` access to the cluster.
