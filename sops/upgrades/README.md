# Openshift 4 Upgrades

## Announcing maintenance/version upgrades
There is currently no formal process for announcing maintenance/upgrades for the Centos CI Openshift 4 infrastructure. Until this gets fleshed out fully, proceed with the following process. Openshift 4 upgrades aim to be seamless and non service affecting, end users should hopefully suffer nothing more than temporary slowness in cluster responsiveness.

- Roll updates out to staging first. Roll updates out to production a day or two later, if everything is still working as expected.
- Announce via email list: `ci-users@centos.org`, and via IRC: `centos-ci@irc.libera.chat` that a maintenance period or upgrade will be taking place and when.
- Ensure that the time allotted for maintenance/upgrade is adequate to complete with some leeway for encountering issues. eg 2 hours
- Once complete, or if the maintenance/upgrade is cancelled, inform via email list `ci-users@centos.org`, and via IRC on `#centos-ci@irc.libera.chat` once more.

## Steps
* Silence alerting to prevent oncall engineers getting pinged when services/nodes restart during the upgrade.
* Login with Admin access account and go in `Administration` section, then `Cluster Settings`.
* Ensure that the channel is set to `stable-major.minor` and that the `Update now` button is visible.
* If you are updating to a new minor version, you must first update the channel to `fast-major.minor` version which matches the minor version you wish to update to. Follow the latest information on [2] for updating between minor/major versions, as there might be information such as backing up important resources prior to an update.
* Now click update now, and choose the latest `major.minor.patch` version.
* Keep an eye on the status of the upgrade to ensure it does not stall/fail half way through.
* Perform suite of tests (functional/integration) to ensure the cluster is still working as expected.
* Unsilence monitoring/alerting once the cluster has completed with the upgrade.



Resources
*   [1] [https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html](https://docs.openshift.com/container-platform/4.4/updating/updating-cluster-cli.html)
*   [2] [https://docs.openshift.com/container-platform/4.5/updating/updating-cluster-between-minor.html](https://docs.openshift.com/container-platform/4.5/updating/updating-cluster-between-minor.html)
