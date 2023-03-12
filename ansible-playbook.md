# Ansible Playbook

Ansible palybooks are ansible orchestration language, it is in playbook we define what we want ansible to do, it is a set of instruction you provide ansible to work it's magic.

For example

*	It can be as simple as running series of commands on different servers in a sequence and restarting those servers in a particular orders.
* Or, It can be as complex as deploying hundreds VM in public and private cloud infrastrucutre provisioning storage to VMs, setting up their networks and cluster configurations, configuring complex applications on them such webservers or database servers, setting up load balancing, setting up monitoring components, installating and configuring backup clients and update configuration database with information about the new VMs, etc

Remember all playbooks are written in YAML format. It is single yaml file containing set of plays, a play define set of activities to be run on a single or group of hosts. a task is a single action to be performed on host example running a command or  a script on the host, installing a package in host, performing a shutdown or resart operation. 

Example Playbook.yml
```YAML
-
  name: Play 1
  hosts: localhost
  tasks: 
	- name: Execute command 'date'
	  command: date

	- name: Execute script on server
	  script: test.sh

	- name: Install httpd service
	  yum:
		name: httpd
		state: present

	- name: Start web server
	  service:
		name: httpd
		state: started
```
This playbook contains a single play named `Play 1` the goal of this play is to run a set of activities one after another on the localhost. Remember `hosts` is defined in a play level, we want to test it in localhost that's why it is set to `localhost` but it can be anything host from your inventory. 

Example2: Playbook.yml - List of 2 Plays
```YAML
-
  name: Play 1
  hosts: localhost
  tasks: 
	- name: Execute command 'date'
	  command: date

	- name: Execute script on server
	  script: test.sh

-
  name: Play 2
  hosts: localhost
  tasks:
	- name: Install httpd service
	  yum:
		name: httpd
		state: present

	- name: Start web server
	  service:
		name: httpd
		state: started
```

> Here we have list of dictionaries.

Make sure here host or group defined in play are present in inventory file, as all info about host is taken from inventory. If we specify group instead then the listed tasks are executed on all the hosts listed under that group simultaneously. 

#### Modules 

The different action run by tasks are called modules in this case, command, script, yum and service are ansible modules, there are hundreds of module avaiable out of the box. 
you can use `ansible-doc -l` to get familiar with playbook structure. 

To execute the ansible playbook:
```bash
ansible-playbook playbook.yml
```

## Ways of running ansible

1. Using the ansible command.
2. Using  the ansible-book command.

Sometimes you may want to run a task just to test the connectivity,
In this case we can use ansible command instead of writing playbooks:
*	Reboot 
```bash
ansible <hosts> -a <command>
ansible all -a "/sbin/reboot"
```
*	Ping host
```bash
ansible <hosts> -m <module>
ansible target1 -m ping
```

These are imerative style of execution there are no playbooks involved you are running seperate ansible commands for each operation.

The real usage of ansible is with playbook. So there is a declarative approach, the playbook now can be saved on source code repositories like github and managed centrally. Ansible playbook command need a playbook file.

---

#### All group in ansible
There is a group ansible creates by default and that's called `all` group and all group simply means that it is an builtin group that ansible creates and it has all the servers in our inventory file are part of group.

1.	Using Ansible command: 
```bash
ansible all -m ping -i inventory.txt
```

Output: 
```
target2 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

2.	Using Playbook:
 
 *	playbook-ping.yaml: 
```YAML 
-
  name: Test connectivity to target servers
  hosts: all
  tasks:
    - name: Ping test
      ping:
```

  *	Execute playbook:
  ```bash
  ansible-playbook playbook-ping.yaml -i inventory.txt
  ```

Output:
```

PLAY [Test connectivity to target servers] *****************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
fatal: [target2]: FAILED! => {"msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."}
ok: [target1]

TASK [Ping test] *******************************************************************************************************************************************************************************
ok: [target1]

PLAY RECAP *************************************************************************************************************************************************************************************
target1                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target2                    : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

> Here we got falied in first task because of ssh fingerprint key. we can fix it by accepting the key by making a ssh connection to that host. 


