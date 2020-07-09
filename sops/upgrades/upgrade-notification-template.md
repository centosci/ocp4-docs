# Upgrade email notification template
The purpose of this template is to provide an easy way to copy and paste, to create an upgrade notification email which can be shared on our mailing lists such as the `ci-users@centos.org` list, when we are rolling updates out to the CentOS CI OCP4 cluster.


```
Subject: Scheduled <MAINTENANCE/UPGRADE> Notification: CentOS CI <STAGING/PROD> Openshift Cluster 


Dear CentOS CI User,

Please be advised that the CentOS CI <STAGING/PROD> Openshift cluster is due for scheduled maintenance from <START_TIME> to <ESTIMATED_END_TIME> UTC on <DATE>. During this period necessary updates will be applied to the cluster, and will be upgraded from version <VERSION_OLD> to <VERSION_NEW>.

The upgrade is not expected to be service affecting, however there might be periods of slight performance degradation during this maintenance period. We apologise for any inconvenience that this may cause.
 

Kind regards,
CentOS CI Infrastructure.
```

Once the upgrade has completed, follow up with a reply to notify users that the work is complete.

```
Dear CentOS CI User,

This scheduled maintenance is now completed. Thank you for your patience.


Kind regards,
CentOS CI Infrastructure.
```
