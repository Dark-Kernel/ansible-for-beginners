# Ansible Variable

Just like any other scripting or programming language variables are used store values that varies with different items. 

we have already worked with variable in `Invetory.txt`

Inventory.txt: 
```
web1 ansible_host=server1.domain.com ansible_connection=ssh ansible_ssh_pass=pass
web2 ansible_host=server2.domain.com ansible_connection=ssh ansible_ssh_pass=pass2 
```
Here `ansible_host` `ansible_connection` and `ansible_ssh_pass` are variables which stores the values.

###### 
Playbook.yml:
```YAML
name: Add DNS server to resolv.conf
hosts: localhost
vars: 
  dns_server: 10.1.250.9
tasks:
  - lineinfile:
	  path: /etc/resolv.conf
	  lines: 'nameserver {{ dns_server }}'
```

In this case `dns_server` is the variable. we can also a variable file dedicated for only variables.
To use the variable simply enter the variable name inside double curly braces `{{ }}` and when we run the playbook ansible will replace it with value in the variable.

######

### Example 1: 

This is a playbook used to set multiple firewall configurations which has a number of values hard coded in it.
```YAML
-
  name: Set Firewall Configurations
  hosts: web
  tasks:
  - firewalld:
	  service: https
	  permanent: true
	  state: enabled

  - firewalld:
	  port: 8081/tcp
	  permanent: true
	  state: disabled

  - firewalld: 
	  port: 161-162/udp
	  permanent: true
	  state: disabled

  - firewalld:
	  source: 192.0.2.0/24
	  Zone: internal
	  state: enabled

```

If someone else want to reuse this playbook, he would have to modify the playbook to change these values, if these values that can vary or move to the Inventory file and refer to it in the playbook using the `{{  }}` we could get away with modifying the inventory file alone in the future and not have to modify the playbook itself. 

And even better way to do this would be to move the variable to the file in the name of the host. 

> Remember the format we are using use variable in playbooks is called jinja2 Templating. while using variable remeber to enclose it within quotes if you are start your assingment with variables. 

######
### Example 2: 

Use the when conditional to print if I am a child or an Adult based on whether my age is < 18 (Child) or >= 18 (Adult).

```YAML 
- name: 'Am I an Adult or a Child?'
  hosts: localhost
  vars:
    age: 25
  tasks:
    - name: I am a Child
      command: 'echo "I am a Child"'
      when: 'age < 18'
    - name: I am an Adult
      command: 'echo "I am an Adult"'
      when: 'age >= 18'
```


######
### Example 3:



The first task in the playbook is using the shell module to display the existing contents of /etc/resolv.conf file and the second one is adding a new line containing the name server details into the file. However, when this playbook is run multiple times, it keeps adding new entries of same line into the resolv.conf file. 

```YAML
---
- name: 'Add name server entry if not already entered'
  hosts: localhost
  become: yes
  tasks:
    - shell: 'cat /etc/resolv.conf'
      register: command_output
    - shell: 'echo "nameserver 10.0.250.10" >> /etc/resolv.conf'
      when: 'command_output.stdout.find("10.0.250.10") == -1'

```
