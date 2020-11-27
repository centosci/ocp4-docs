# Cleaning jenkins storage
When recieving Zabbix alerts for low storage on the legacy Jenkins, we can prune old builds from some of the largest storage users on the cluster using this SOP.


## Step 1: Creating a token
* Firstly generate a jenkins token go to `https://ci.centos.org/user/username/configure`
* Create a token from API token section
* Set the username and token variables below

```
JENKINSUSER=username
JENKINSAPITOKEN=token
```


## Step 2: Getting list of jobs
* ssh into `jenkins.ci.centos.org`
* Find the list of projects which are consuming most space by `du -csh /var/lib/jenkins/* | grep 'G' | sort -r`


## Getting crumb api
* Use curl to generate a Crumb token 

```
CRUMB=$(curl 'https://$JENKINSUSER:$JENKINSAPITOKEN@ci.centos.org/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
```


## Deleting builds from job
* Now with the crumb token set, we can delete the jobs using the API.
* In the following example, update the `jobname` and `start range/ end range` values which correspond with the build numbers in the jobname:

```
curl -H "$CRUMB" -X POST "https://$JENKINSUSER:$JENKINSAPITOKEN@ci.centos.org/job/<jobname>/[<start>-<end>]/doDelete"
```
