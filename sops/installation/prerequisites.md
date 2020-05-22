## Prerequisites
The following are the prerequisites required to install OCP4 on bare metal

### Access confirmation

*   Access to [https://access.redhat.com/](https://access.redhat.com/), if not, follow the steps
    *   [https://mojo.redhat.com/docs/DOC-99172](https://mojo.redhat.com/docs/DOC-99172)
    *   [https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit](https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit)
*   Git repo for the installer [https://github.com/openshift/installer](https://github.com/openshift/installer)
*   OpenShift playground: [https://try.openshift.com](https://try.openshift.com/)
*   Access.redhat.com account, to download packages/pull secrets [https://cloud.redhat.com/openshift/install](https://cloud.redhat.com/openshift/install)
    *   openshift-install client: [https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux.tar.gz](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux.tar.gz)
    *   RHCOS download to create machines for your cluster to use during the installation [https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/latest/](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/latest/)
    *   Openshift Command Line tools: [https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz)
*   Official documentation for installation: 
    *   [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html)
    *   [https://docs.openshift.com/container-platform/4.3/architecture/architecture-installation.html#architecture-installation](https://docs.openshift.com/container-platform/4.3/architecture/architecture-installation.html#architecture-installation)
*   Access RH employee subscription benefits: \
[https://mojo.redhat.com/docs/DOC-99172 \
https://docs.google.com/document/d/15DmYrfspKVwf4z8DzPK7sU-zmRERVWHBN3tghRkrkGU/edit](https://mojo.redhat.com/docs/DOC-99172)


### Bootstrap node Identification
As per [1], the minimum number of nodes needed for an Openshift 4 cluster is 6

*   1 bootstrap node
*   3 master nodes
*   2 worker nodes.

As per [2] the minimum requirements for the bootstrap machine is:

<table>
  <tr>
   <td>
    <strong>Machine</strong>
   </td>
   <td>
    <strong>Operating System</strong>
   </td>
   <td>
    <strong>vCPU</strong>
   </td>
   <td>
    <strong>RAM</strong>
   </td>
   <td>
    <strong>Storage</strong>
   </td>
  </tr>
  <tr>
   <td>
    Bootstrap
   </td>
   <td>
    RHCOS
   </td>
   <td>
    4
   </td>
   <td>
    16 GB
   </td>
   <td>
    120 GB
   </td>
  </tr>
</table>


*   [1] Minimum number of nodes [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#machine-requirements_installing-bare-metal](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#machine-requirements_installing-bare-metal)
*   [2] Minimum bootstrap/master/worker node requirements [https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#minimum-resource-requirements_installing-bare-metal](https://docs.openshift.com/container-platform/4.3/installing/installing_bare_metal/installing-bare-metal.html#minimum-resource-requirements_installing-bare-metal)
*   [3] [https://ark.intel.com/content/www/us/en/ark/products/64591/intel-xeon-processor-e5-2640-15m-cache-2-50-ghz-7-20-gt-s-intel-qpi.html](https://ark.intel.com/content/www/us/en/ark/products/64591/intel-xeon-processor-e5-2640-15m-cache-2-50-ghz-7-20-gt-s-intel-qpi.html)


### Miscellaneous Prerequisites

*   Need internet access from the bootstrap/master/compute nodes so as to:
*   Access the Red Hat OpenShift Cluster Manager page to download the installation program and perform subscription management and entitlement. If the cluster has internet access and you do not disable Telemetry, that service automatically entitles your cluster. If the Telemetry service cannot entitle your cluster, you must manually entitle it on the Cluster registration page
*   Access quay.io to obtain the packages (images?) that are required to install your cluster.
*   Obtain the packages that are required to perform cluster updates.
*   **1.1.3.1**. Before you install OpenShift Container Platform, you must provision two layer-4 load balancers.
*   Minimum of 6 nodes, 1 bootstrap node, 3 master, 2 compute.
*   **1.1.3**. See this section to see the **network ports** which are required to be open and accessible from each machine
*   Configure DHCP or set static IP addresses on each node. Be sure to configure it so the nodes always get the same IP address if configured via DHCP.
