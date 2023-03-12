# Ansbile roles

Just like how you would assign roles to different people in the real world making them doctor, engineers, Astronauts, policeman, chef, etc. In the ansible world you would assign an roles to blank servers to make them a database server, webserver, redis messaging server, backup server or montioring server. Assigning a role in the real world means doing everying when needs to do to make someone a doctor or engineer.


In the automation world assigning a roles means doing everything you need to do to make a server, like in a database server such as -
  *	 Installing prerequistes for mysql. 
  *  Installing mysql packages.
  *	 Configuring mysql service.
  *	 Configuring database and users.

With the webserver it would be again:
  * Installtion pre-requisites.
  * Installing nginx packages.
  * Configuring nginx service.
  * Configuring custom web pages.


Here's a simple playbook that can do this:

```YAML
-  name: Install and Configure MySQL
   hosts: db-server
   tasks: 
     - name: Install pre-requisites 
	   yum: name=pre-req-packages  state=present

     - name: Install mysql packages
	   yum: name=mysql state=present

	 - name: Start MySQL servic
	   service: name=mysql  state=started

  	 - name: Configure Database
	   mysql_db: name=db1  state=present
```

We can do task easily with is playboook. Then why we need a role? 

These set of tasks to install and prerform basic configuration on mysql databse is going to remain mostly common, once a person develop a playbook it can be shared with 100s and 1000s of other trying to do the same thing, instead of all rewriting same code you could package it into a role and reuse it later. next time you can simple assign the role you created and in a playbook be it a single server or 100s of servers that all we need.


So this is the primary purpose of roles to make your work reusable, for your organization or for others at global level. It also help organizing your code within ansible

You can find tons roles on [ansible galaxy](https://galaxy.ansible.com/).


### How to get start with roles?

1. Directory structure: You don't have to do that manually ansible-galaxy has a neet tool for it. Use the below command to create skeleton: 

    ```bash
   ansible-galaxy init mysql
	```
	Remember this is to create your own role from scrach. Then move all your code into `tasks` directory.


2. How to use roles within your playbook

	```YAML
	- name: Install and configure MySQL
	  hosts: db-server
	  roles: 
	    - mysql
	```

3. How playbook will find that roles ?

	There are two ways for that:
	  1. Create `roles` directory within your playbook directory, and move the role created under it.
	  2. You can move the roles to a common direcory designated for roles on your system at `/etc/ansible/roles/` that's the default location where ansible searches for roles. You can check path `roles_path` in `/etc/ansible/ansible.cfg`.

4. How to share with community? 

	You can share with your community by uploading it to ansible galaxy through a github repository. 

5.  How to find roles? 

	1. You can find roles from galaxy UI 
	2. You can use `ansible-galaxy` command to search from cli.

		  ```bash
		  ansible-galaxy search mysql
		  ```
	3. To use the role install it using the below command:
		
		```bash
		ansible-galaxy install geerlingguy.mysql
		```
		Install locally in current roles directory
		```bash
		ansible-galaxy install geerlingguy.mysql -p ./roles
		```

6. How to it after installation?

	Use the same name of role as installed package.

	```YAML
	-
	  name: Install and Configure MySQL
	  hosts: db-server
	  roles: 
	    - geerlingguy.mysql
	```

	Use below command to list installed roles:

	```bash
	ansible-galaxy list
	```

	Check the ansible configuration using:

	```bash
	ansible-config dump | grep ROLE
	```



