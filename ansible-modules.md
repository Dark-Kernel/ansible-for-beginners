# Ansible Modules

Ansible modules are categories into various group based on there functionality, some of them listed here:
1.	System:
	  System modules are actions to be performed at system levels, such as modifying users and the groups, modifying iptables, firewall configuration, working with logical groups, mounting operations, or working with services like starting, stopping, restart.

	- User 
	- Group
	- Hostname
	- Iptables
	- Lvg
	- Lvol
	- Make
	- Mount 
	- Ping
	- Timezone
	- Systemd
	- Service:The service module is used to manage services on a system starting, stopping, restarting. 

	  - name: Name of the service.
	  - state: Operation we would like to perform. 

		We can write this in two formats:
		
		```YAML
		-
		  name: Start Services in order
		  hosts: localhost
		  tasks: 
			- name: Start the database service
			  service: name=postgresql state=started
		```
		Or in a Dictionary/Map format:
		```YAML
		-
		  name: Start Services in order
		  hosts: localhost
		  tasks: 
			- name: Start the database service
			  service:
				name: postgreql
				state: started
		```

		Now the question may arise that why state is "started" and not "start"?
		
		Reason: we are not instructing ansible to start the serivce instead we are instructing to ensure that the serivce is started, if not then start it, if already started do nothing. This is called idempotency. 


######
*	Commands: Commands modules are used to execute commands or scripts on host, This could be simple commands using the `Command` modules or an interaction excution using the `Expect` module by responding to prompts, we can also execute scripts on host using `Script` module.

	- **command** parameters: 
		
		- chdir: cd in this dir before execute the command.
		  
		  ```YAML
		  ...
		  name: Display resolv.conf content.
		  command: cat resolv.conf chdir=/etc
		  ... 
		  ```
		- creates: Use to perform a check before the command is run.

		  ```YAML
		  ...
		  name: Create mydir directory if does not exists.
		  command: mkdir /mydir creates=/mydir 
		  ...
		  ```
		- free_form: we cannot you this as `chdir` or `creates`, Our command inputs like command inputs are free_form like the above commands.  Not all modules have free form input like this like `copy` module:
		  ```YAML
		  ...
		  - name: Copy file.
			copy: src=/etc/file dest=/dest
		  ...
		  ```
		  In above example `copy` command only takes parameterized input and not free form input. command in a  command module is a free form.

	- Expect 
	- Raw
	- Script: Executes script which is located locally on the ansible controller machine on one or more remote nodes after transferring it over, To run a script on hundreds of server you really don't have to copy on all the servers ansible will takes the care of it automatically copying the script to node and execute them on hosts.
	  ```YAML
	  - 
		name: Play 1
		hosts: localhost
		tasks: 
		  - name: Run a script on remote server
			script: /some/local/script.sh -arg1 -arg2
	  ```
	- Shell
######

*	Files: File module helps works with files, Use the `Acl` module to set and retrieve ACl information on files, Use the `Archive` and `Unarchive` module to compress and unpack files, Use `Find`, `Lineinfile` and `Replace` modules to modify the content of existing file. 

	- Acl
	- Archive
	- Copy
	- File
	- Find
	- Lineinfile: Used to find a line in any file and replace it or add it if it doesn't already exists. 

	  ```YAML
	  -
		name: Add DNS server to resolv.conf
		hosts: localhost
		tasks:
		  - lineinfile:
			  path: /etc/resolv.conf 
			  line: 'nameserver 10.1.250.10'
			  create: yes  # if file !exist create it 
	  ```

	  The above playbook will ensure that this line exists in resolv.conf, if not then it will add it and if it already exists then it will do nothing.

	  
	  If we try to do the same thing with script:
	  ```bash
	  #sample script

	  echo "nameserver 10.1.250.10" >> /etc/resolv.conf 
	  ```
	  This will append the same nameserver multiple time whenever we run the script, that's why ansible used <mark style="background-color:grey">  idempotency </mark> .

	- Replace
	- Stat
	- Template
	- Unarchive

######
*	Database: Database module help in working with database such as:
	- Mongodb
	- Mssql
	- Mysql
	- Postgresql
	- Proxysql
	- Vertica

######
*	Cloud: The cloud section has a vast collection of modules for various different cloud providers, There are number of modules available for each of the is allow you to perform various tasks such as creating and destroying instance, performing configuration changes in networking and security, managing container and data centers clusters, virtual networking and a lot more:
	- Amazon
	- Atomic
	- Azure
	- Centrylink
	- Cloudscale
	- Cloudstack
	- Digital Ocean
	- Docker
	- Google
	- Linode
	- Openstack
	- Rackspace
	- Smartos
	- Softlayer
	- VMware

######
*	Windows: Helps you to use ansible in windows environment, some of them are `Win_copy` to copy files, `Win_command` to execute a command on a windows machine and there are bunch of other modules to work with files in windows, create a IIS website, install software using `Win_msi`, make changes into registeries using `Win_regedit` and manage services and user in windows, these are just few modules, there are lot more.

	- Win_copy
	- Win_command
	- Win_domain
	- Win_file
	- Win_iis_website
	- Win_msg
	- Win_msi
	- Win_package
	- Win_ping
	- Win_path
	- Win_robocopy
	- Win_regedit
	- Win_shell
	- Win_service
	- Win_user

######
*	More.. @ docs.ansible.com with detailed instruction of each of them.
