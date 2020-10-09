# CentOS Infra Onboarding

### Mailing lists

- https://lists.centos.org/mailman/listinfo/ci-users
- https://lists.centos.org/mailman/listinfo/centos-devel
- https://lists.centos.org/mailman/listinfo/centos-infra
- ci-sysadmin@centos.org

### IRC Channels
- #centos-ci
- #centos
- #centos-meeting

### Openshift
Containers

STG:
3 master/control nodes
5 worker/compute

Prod:
3 master/control
9 worker/compute


### Open Nebula
Application for managing VM instances
GUI is only available within the network, can do things like ssh port forwarding to access the console

- 1 frontend control node
- 9 hypervisor nodes



### Duffy
Application for managing Bare metal nodes

Rest API
beanstalkd message bus
Ansible playbooks, IPMI, PXE
API client python-cicoclient
Reserve a bare-metal for 6 hours
We maintain multiple versions of CentOS several different architectures for baremetal nodes in a pool ready to be consumed
eg: CentOS 6, 7, 8, 8-stream.

Architecture node counts in pool:

```
x86-64 83
i386 7
ppc64 / ppc64le 45
aarch64 20
```

- Duffy runs on: admin.ci.centos.org
- https://github.com/centos/duffy


### Legacy and (Legacy Legacy)
- OKD 3.6 https://console.apps.ci.centos.org:8443/
- Jenkins: https://ci.centos.org/


### Resources

- Working with CentOS Infra https://docs.fedoraproject.org/en-US/cpe/day_to_day_centos/
- Ticket Tracker: pagure.io/centos-infra [Use template ci-migration for migration or 

- Fedora Nest 2020 talk: https://docs.google.com/presentation/d/1Efwz73t4NdxuzmyV8kvi3SewCIzDpidpePXGLfuweso/edit?ts=5f2ea19e#slide=id.g8fead7ec8b_0_44

- CentOS ACO (FAS): https://accounts.centos.org/
- SSH Config Jump host: https://wiki.centos.org/TipsAndTricks/SshTips/JumpHost

- CentOS Infra Playbooks https://github.com/CentOS/ansible-infra-playbooks
- Inventory: https://scm.infra.centos.org/CentOS/ansible-inventory-ci
- pkistore: https://scm.infra.centos.org/CentOS/ansible-pkistore-ci
- filestore: https://scm.infra.centos.org/CentOS/ansible-filestore-ci

- WIKI: https://wiki.centos.org/

- Centos QA Kanban: https://apps.centos.org/kanboard/board/28
- Centos QA Jenkins: https://console.qa.centos.org/

- Openshift 4 SOPs/Spikes: https://github.com/centosci/ocp4-docs
- OCP4 Staging: https://console-openshift-console.apps.ocp.stg.ci.centos.org/
- OCP4 Production: https://console-openshift-console.apps.ocp.ci.centos.org/
