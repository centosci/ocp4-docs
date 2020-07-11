# Action
If you are currently using apps.ci.centos.org (OCP 3.6) or have a job running in ci.centos.org, please contact us so that we can create a namespace for you in the new OCP 4.4 cluster for you to start migrating your jobs.


## If your own a namespace on apps.ci.centos.org
There shouldn't be a lot of changes for you as you should already have a familiar workflow working with Openshift, if you have configured your jobs at least once. While opening a ticket, or reaching out to us please do mention if you need ability to checkout duffy nodes (if yes, do you already have a duffy API key?).

See the following which explains how to open a ticket (Point 3)


## If your project is on ci.centos.org
As you are using central jenkins deployment that's not private to you, your project might see some changes. You will have your own jenkins deployment to run all your jobs instead of having a shared jenkins instance where you don't have admin access. With the extra privilage in jenkins, you can add plugins, credentials as you need and a bunch of other abilities.
This also means there is no direct maintenance required from CentOS CI Infra admins as openshift takes care of things dying or crashing and anything in the jobs config, you can take care of them yourself (We will help you get started and are always in reach if you notice any hiccups that is not auto healed). You can have the same workflow or use this opportunity to change your freestyle jobs to pipelines but that's totally on you.
See the following which explains how to open a ticket (Point 3)


### open a project ticket

Create a new ticket in [pagure.io/centos-infra/issues](https://pagure.io/centos-infra/issues) with type ci-migration
template or [click here](https://pagure.io/centos-infra/new_issue?template=ci-migration).

We will have your accounts created in 48 hours (if no doubts/followup needed).

Note: This is only applicable for already existing projects in other places. New projects will have to go through a different process/evaluation and we may need more time (this is also applicable for projects requesting extra privilages than default admin level access to namespace).


## Setting up your jobs in cico-workspace jenkins
Configuring your jobs should be similar to your older configuration. You can either have your jobs written in trigger jobs -> groovy dialogue box, or source it from your repo (we recommend the later for easy management of jenkinsfile). One thing to point, in groovy, the node parameter type defines where the target job should be executed, the value must match either a label or a node name - otherwise the job will just stay in the queue.
We have a custom label called `cico-workspace` that has python-cicoclient installed in it so that you can request duffy nodes. This workspace will also have your duffy-api-key exported so that you can directly request nodes from duffy. Here is an example of jenkins file
```
node('cico-workspace') {
    stage('get cico node') {
		node = sh(script: "cico node get --release=8", returnStdout: true).trim().tokenize(' ')
		env.node_hostname = "${node[0]}.ci.centos.org"
		env.node_ssid = "${node[1]}"
	}

    stage('tests-example') {
        pass
    }

    stage('builds-example') {
        pass
    }

    finally {
        stage('return cico node') {
			sh 'cico node done ${node_ssid}'
        }
    }
}
```

