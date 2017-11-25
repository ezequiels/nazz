###Playbook

```
- hosts: server.domain.com
  strategy: debug
  gather_facts: no
  vars:
    var_path: '/root/test.txt'
    var_og: 'root'
    var_mod: 0644
    var_state: 'touch'
  tasks:
       - name: Create File
         file:
           path: '{{ var_path }}'
           owner: '{{ var_og }}'
           group: '{{ var_og }}'
           mode: '{{ var_mode }}'
           state: '{{ var_state }}'
```

### ansible command
```
ansible-playbook playbook.yml -u root
```
### Debug Execution

```
PLAY [server.domain.com] ****************************************************************************

TASK [Create File] *************************************************************************************
fatal: [server.domain.com]: FAILED! => {"msg": "The task includes an option with an undefined variable. The 
error was: 'var_mode' is undefined. The error appears to have been in '/Users/zeke/git/python/ansible
/playbook.yml': line 10, column 10, but may be elsewhere in the file depending on the exact syntax problem. 
The offending line appears to be: tasks:
       - name: Create File        ^ here"}
Debugger invoked
(debug) p task.args
{u'group': u'{{ var_og }}',
 u'mode': u'{{ var_mode }}',
 u'owner': u'{{ var_og }}',
 u'path': u'{{ var_path }}',
 u'state': u'{{ var_state }}'}
(debug) task.args['mode'] = '{{ var_mod }}'
(debug) p task.args
{u'group': u'{{ var_og }}',
 u'mode': '{{ var_mod }}',
 u'owner': u'{{ var_og }}',
 u'path': u'{{ var_path }}',
 u'state': u'{{ var_state }}'}
(debug) redo
changed: [server.domain.com]

PLAY RECAP *********************************************************************************************
server.domain.com       : ok=1    changed=1    unreachable=0    failed=0

```


### Inventory example

```
[monitoring]
nms.thepalace.local
kibana.thepalace.local
icinga2.thepalace.local
sensu.thepalace.local
[cicd]
gocd.thepalace.local
rundeck.thepalace.local
jenkins.thepalace.local
gitlab.thepalace.local
[media]
plexlx.thepalace.local
[db]
influxdb.thepalace.local
elastic.thepalace.local
[chef]
chefclient.thepalace.local
[containers]
docker.thepalace.local
kubernetes.thepalace.local
kubernodes.thepalace.local
[webservers]
apache1.thepalace.local
apache2.thepalace.local
```

YAML

```
all:
  hosts:
    monitoring:
      hosts:
        nms.thepalace.local
        kibana.thepalace.local
        icinga2.thepalace.local
        sensu.thepalace.local
    cicd:
      hosts:
        gocd.thepalace.local
        rundeck.thepalace.local
        jenkins.thepalace.local
        gitlab.thepalace.local
    media:
      hosts:
        plexlx.thepalace.local
    db:
      hosts:
        influxdb.thepalace.local
        elastic.thepalace.local
    chef:
      hosts:
        chefclient.thepalace.local
    containers:
      hosts:
        docker.thepalace.local
        kubernetes.thepalace.local
        kubernodes.thepalace.local
    webservers:
      hosts:
        apache1.thepalace.local
        apache2.thepalace.local

```

### Playbook Example

```
---
- hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure Apache is at the latest version
    yum: name=httpd state=latest

- hosts: db
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    yum: name=postgresql state=latest
  - name: Ensure that PostgreSQL is started
    service: name=postgresql state=started

```

### Playbook Example 2

```
- hosts:
    - all
  tasks:
    - action: ping
```