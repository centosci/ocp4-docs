# SOP to Create a duffy API/SSH keys
This SOP covers the process of creating an API key for duffy, and adding it to the duffy database table


## Requirements

- project name

## Duffy Database Schemas

```
MariaDB [duffy]> show tables;
+-----------------+
| Tables_in_duffy |
+-----------------+
| alembic_version |
| session_archive |
| session_hosts   |
| sessions        |
| stock           |
| userkeys        |
| users           |
+-----------------+
7 rows in set (0.00 sec)

MariaDB [duffy]> describe stock;
+--------------+--------------+------+-----+---------+-------+
| Field        | Type         | Null | Key | Default | Extra |
+--------------+--------------+------+-----+---------+-------+
| id           | int(11)      | NO   | PRI | NULL    |       |
| hostname     | varchar(20)  | YES  |     | NULL    |       |
| ip           | varchar(15)  | YES  |     | NULL    |       |
| chassis      | varchar(20)  | YES  |     | NULL    |       |
| used_count   | int(11)      | YES  |     | NULL    |       |
| state        | varchar(20)  | YES  |     | NULL    |       |
| comment      | varchar(255) | YES  |     | NULL    |       |
| distro       | varchar(20)  | YES  |     | NULL    |       |
| rel          | varchar(10)  | YES  |     | NULL    |       |
| ver          | varchar(10)  | YES  |     | NULL    |       |
| arch         | varchar(10)  | YES  |     | NULL    |       |
| pool         | int(11)      | YES  |     | NULL    |       |
| console_port | int(11)      | YES  |     | NULL    |       |
| flavor       | varchar(20)  | YES  |     | NULL    |       |
| session_id   | varchar(37)  | YES  | MUL | NULL    |       |
| next_state   | varchar(20)  | YES  |     | NULL    |       |
+--------------+--------------+------+-----+---------+-------+
16 rows in set (0.01 sec)

MariaDB [duffy]> describe users;
+-------------+-------------+------+-----+---------+-------+
| Field       | Type        | Null | Key | Default | Extra |
+-------------+-------------+------+-----+---------+-------+
| apikey      | varchar(37) | NO   | PRI |         |       |
| projectname | varchar(50) | YES  |     | NULL    |       |
| jobname     | varchar(50) | YES  |     | NULL    |       |
| createdat   | date        | YES  |     | NULL    |       |
| limitnodes  | int(11)     | YES  |     | NULL    |       |
+-------------+-------------+------+-----+---------+-------+
5 rows in set (0.00 sec)

MariaDB [duffy]> describe userkeys;
+------------+---------------+------+-----+---------+----------------+
| Field      | Type          | Null | Key | Default | Extra          |
+------------+---------------+------+-----+---------+----------------+
| id         | int(11)       | NO   | PRI | NULL    | auto_increment |
| project_id | varchar(37)   | YES  | MUL | NULL    |                |
| key        | varchar(8192) | YES  |     | NULL    |                |
+------------+---------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

MariaDB [duffy]> 

```


```
+-----------+----------------------+----------------------+------------+-------------+
| apikey    | projectname          | jobname              | createdat  | limitnodes |
+-----------+----------------------+----------------------+------------+-------------+
| xxxx-yyyy | nfs-ganesha          | nfs-ganesha          | 2016-02-24 |         10 |
| zzzz-aaaa | CentOS               | centos_arrfab        | 2015-04-17 |         10 |
+-----------+----------------------+----------------------+------------+-------------+
```

## Steps to create a new duffy SSH key
1. On the home directory of user duffy on the admin.ci.centos.org instance, we have a folder where we store the created ssh keys for duffy tenants.
2. `mkdir -p keys/project-name/` then `ssh-keygen -f ~duffy/keys/project-name/id_rsa -C project-name@CI`
3. Copy the public key

## Steps to create a new duffy API key

1. How do we connect to instances

The Duffy database runs on the admin.ci node: `ssh admin.ci.centos.org`. 

2. We have a script which does this work.. how do we use it

3. Create user in usertable
`insert into users values(UUID(), 'projectname', 'projectname', NOW(), 5);`

4. Retrieve the api key from the users table
` select * from users where projectname="projectname";`

5. Using that api-key/UUID as project_id, enter ssh key of a user from the project so that they can ssh into the machines. This process must be repeated for every user we wish to add access to via SSH.
`insert into userkeys (`project_id`,`key`) values('<project-UUID>', '<ssh-key>');`
This ssh key is pushed to duffy nodes - authorized keys when a tenant requests the node through api key.
