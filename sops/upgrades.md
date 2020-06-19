# Openshift 4 Upgrades

## Announcing maintenance/version upgrades
There is currently no formal process for announcing maintenance/upgrades for the Centos CI Openshift 4 infrastructure. Until this gets fleshed out fully, proceed with the following process. Openshift 4 upgrades aim to be seamless and non service affecting, end users should hopefully suffer nothing more than temporary slowness in cluster responsiveness.

- Announce via email list: `ci-users@centos.org`, and via IRC: `centos-ci@freenode` that a maintenance period or upgrade will be taking place and when.
- Ensure that the time allotted for maintenance/upgrade is adequate to complete with some leeway for encountering issues. eg 2 hours
- Once complete, or if the maintenance/upgrade is cancelled, inform via email list `ci-users@centos.org`, and via IRC on `centos-ci@freenode` once more.

## Steps
* Login with Admin access account and go in `Administration` section, then `Cluster Settings`
* Select Update now (visible if an update is available) and select the version you want to update your cluster to
* Click update now


Resources
*   [1] [https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html](https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html)
