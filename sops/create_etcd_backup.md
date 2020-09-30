# Create etcd backup
This SOP should be followed in the following scenarios:

- When the need exists to create an etcd backup. 
- When shutting a cluster down gracefully.

## Steps

1. Connect to a master node

```
oc debug node/<node_name>
```

2. Chroot to the /host directory on the containers filesystem

```
sh-4.2# chroot /host
```

3. Run the cluster-backup.sh script and pass in the location to save the backup to

```
sh-4.4# /usr/local/bin/cluster-backup.sh /home/core/assets/backup
```

4. Chown the backup files to be owned by user `core` and group `core`

```
chown -R core:core /home/core/assets/backup
```

5. From the admin machine, see inventory group: `ocp-ci-management`, become the Openshift service account, see the inventory hostvars for the host identified in the previous step and note the `ocp_service_account` variable.

```
ssh <host>
sudo su - <ocp_service_account>
```

6. Copy the files down to the admin machine.

```
scp -i <ssh_key> core@<node_name>:/home/core/assets/backup/* ocp_backups/
```


### Resources

- [1] [Creating an etcd backup](https://docs.openshift.com/container-platform/4.5/backup_and_restore/backing-up-etcd.html#backing-up-etcd-data_backup-etcd)
