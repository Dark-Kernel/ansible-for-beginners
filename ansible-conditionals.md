# Ansible Conditionals

### What is a condition?

It can be any check that we perform. such as checking if number is prime or not.

### Example

Here, We have two playbooks that does the same thing, install NGINX on host; But as you know different OS flavour use different package manager. These are two playbooks so you have to choose the right one to respective servers.

```YAML
- name: Instal NGINX
  hosts: 
  tasks:
  - name: Install NGINX on Debian
	apt:
	  name: nginx
	  state: present
```

```YAML
- name: Install NGINX
  hosts: 
  tasks:
  - name: Install NGINX on Redhat 
	yum:
	  name: nginx
	  state: present
```

Now, I want to create a single playbook that works for both of these OS for all hosts. So based on the OS flavour my playbook must run the appropriate tasks, and thats where a conditional statement comes in handy, We could use the `when` conditional statement to specify a conditon for each task only if the condition is true that task is run.


Keywords:

*	`ansible_os_family` is a built in variable that ansible populates with the flavour of OS.
*	Make sure to use `==` double equal sign when checking equality in conditional statement.
*	You may use an `or` operator to satisfy either of the condition.
*	You may use an `and` operator to satisfy both conditions.

such as checking if the os family is debian or redhat 

```YAML
- name: Instal NGINX
  hosts: all
  tasks:
  - name: Install NGINX on Debian
	apt:
	  name: nginx
	  state: present
	when: ansible_os_family == "Debian" and 
	      ansible_distribution_version == "16.04"

  - name: Install NGINX on Redhat 
	yum:
	  name: nginx
	  state: present
	when: ansible_os_family == "Redhat" or 
	      ansible_os_family == "SUSE"
```

### Conditional loops

You may use conditional in loop as well,

Example, Instead of single package, we have a list of packages that needs to be installed, we can have a array named packages that have the list, each item in the list has the name of the package to be installed as well as property called `required`, install packages only the required property is set to true. 

```YAML
- name: Install softwares
  hosts: all
  vars:
    packages:
	  - name: nginx
	    required: True
	  - name: mysql
	    required: True
	  - name: apache 
	    required: False

  tasks:
  - name: Install "{{ item.name }}" on Debian
	apt:
	  name: "{{ item.name }}"
	  state: present
	when: item.required == True
	loop: "{{ packages }}"
```


First we specify the loop directive to execute the install task in a loop the name of the package to be install is `item.name` and this will install all three packages specified 


### Conditional & Register  

To use conditionals with the output of the previous task.

Example, we have a requirement to develop a playbook to check the status of a service an email if it is down. So there are two tasks, first check the status and second sends an email.

> To record the output of one task we could use the register directive, so we say register the output to the result variable.

```YAML
- name: Check status of a service and email if its down
  hosts: localhost
  tasks:
    - command: service https status
      register: result

	- mail:
	    to: admin@company.com
	    subject: Service Alert
	    body: Httpd Service is down
	    when: result.stdout.find('down') != -1

	  
```


