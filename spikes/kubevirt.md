# kubevirt Instruction

`Note: This doc is full of snippets of official doc in order to keep it to point. This is not to be considered a documentation/guide for others. Please refer official guide. This is mere a note for CentOS CI admins based on our workflow`


## How to install Kubevirt in cluster

* Open a browser window and log in to the OpenShift Container Platform web console.
* Navigate to the Operators → OperatorHub page.
* Search for Container-native virtualization and then select it.
* Read the information about the Operator and click Install.
* On the Create Operator Subscription page:
    * For Installed Namespace, ensure that the Operator recommended namespace option is selected. This installs the Operator in the mandatory openshift-cnv namespace, which is automatically created if it does not exist.

    * Select 2.3 from the list of available Update Channel options.

    * Click Subscribe to make the Operator available to the openshift-cnv namespace.

On the Installed Operators screen, the Status displays Succeeded when container-native virtualization finishes installation.

## Deploying container-native virtualization

After subscribing to the Container-native virtualization catalog, create the CNV Operator Deployment custom resource to deploy container-native virtualization.

* Navigate to the Operators → Installed Operators page.
* Click Container-native virtualization.
* Click the CNV Operator Deployment tab and click Create HyperConverged Cluster.
* Click Create to launch container-native virtualization.
* Navigate to the Workloads → Pods page and monitor the container-native virtualization Pods until they are all Running. After all the Pods display the Running state, you can access container-native virtualization.


## creating a vm

* create a vm template (or for testing if kubevirt works in your cluster, you can also use a test template from kubevirt: `https://raw.githubusercontent.com/kubevirt/demo/master/manifests/vm.yaml`)
* once you have your template ready, type `oc create -f <template.yaml>` or for test purpose `oc create -f https://raw.githubusercontent.com/kubevirt/demo/master/manifests/vm.yaml`
* once it returns success, check if the vm is created with `oc get vm`
* Go to webUI to start the vm and you should be able to see all there is to see in a vm.

VMs created are in state off by default. To control them from CLI, you need to install kubevirt-virtctl. Find [instruction here](https://docs.openshift.com/container-platform/4.4/cnv/cnv_install/cnv-installing-virtctl.html#cnv-enabling-cnv-repos_cnv-installing-virtctl)


