# What is ansible?

It automation tool.

uses:
  * configuration
  * Install applications on server
  * configuring firewall on server/VMs
  * we can configure multiple things in one single playbook, like: db,server, etc.

needs: 
  * Uses ssh for the connectivity, no agent needed.


## Environment setup

Using virtual to setup one ansible main machine and two other which will act as server. we will be creating first an template machine and then will clone it for new.

Template machine - 
1.	visit [osboxes](https://www.osboxes.org/virtualbox-images/) to get preconfigured image 
2. 	Download the centos 7.
3. 	Extract the downloaded centos 7z file.
4. 	Open the virtualbox and load the .vhd file of centos.
5.	Start machine and to login you will get user and pass in same page you install centos7z file in info tab.


[^1]: Ansible-Controller machine:

Ansible-Controller machine:

1. Right click on template machine and select create clone.
2. Set name `ansible-controller` and check the reinitialize mac checkbox.
3. Select clone type as `linked clone` 
> Linked clone does not clone enitre virtual machine and consume less space.

Ansible-target1 machine: 

1. same as above one.[^1]


## Inventory file

The information about these target system is wrote in an Inventory file. If you do not create a file ansible uses a default Inventory file `/etc/ansibile/hosts`.
It is an like an .ini like format, simply number of servers are listed one after the others, we can also group together by defining like `[group1]` and define the list of servers in a line below and you can have multiple groups in a single Inventory file.

  1. ansible_hosts : it is Inventory parameter used to specify FQDN or ip address of server.
  2. ansible_connection : Define how it is going to make connection example through ssh, winrm, localhost, etc
  3. ansible_ports : Define on which port to connect to, default is 22 for ssh.
  4. ansible_user: Used to defined the user used to make remote connection like admin, root(default in linux) , etc.
  5. ansible_ssh_pass: Used to set ssh password for linux.


## Ping test

1. create a project directory
```bash
mkdir ping-test
```
2. create a inventory file 
```bash
cd ping-test 
cat > inventory.txt
target1 ansible_host=192.168.0.108 ansible_ssh_pass=osboxes.org
```

3. Now run it with ansible
```bash
ansible target1 -m ping -i inventory.txt
```
4. Output: 
```
target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

If you got error as following
```
target2 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
```
Reason: We need to first accept the key fingerprint of taget machine.
	  it's the same thing we do when doing a ssh connection and typing yes  when prompted.

we can fix this with two ways:

  1. Manually first do an ssh login.
  2. Uncomment `host_key_check` in `/etc/ansible/ansible.cfg`.
  3. Use ssh keys for login instead of passwords.

  
