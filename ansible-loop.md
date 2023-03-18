# Ansible loops

Loop is a looping directive that executes the same task multiple number of time. Each time it runs it stores the value of each item the loop in a variable named `item`.


### Example

Ansible playbook to create user in host:

```YAML
-
  name: Create users
  hosts: localhost
  tasks: 
  -  user: name=jeo	state=present
```

In this case we are creating only one user, but what if we have lots of users to create ?

The better way to do is would be to have a single task loop over all the users, that's where we use loops. You can replace the name with `{{ item }}` 

```YAML
-
  name: Create users
  hosts: localhost
  tasks: 
  -  user: name="{{ item }}"	state=present
	loop:
	  - joe
	  - george
	  - ravi
	  - kishan
	  - kiran 
	  - mike
	  - tyson
	  - shoeb
```

In this case the loop is a array of string values just the username, But what if we want to specify the UID as well? That would means each item in the loop would have to have two values the `username` and `uid`.

But how do you passing two values in an array ? Instead of passing arrays of string in loop we would pass in array of dictionaries, each dictionary will have two key-value pairs. The key would be `name` and `uid` and value will be name and uids of each user:


``` YAML
-
  name: Create users
  hosts: localhost
  tasks: 
  -  user: name="{{ item.name }}"    state=present    uid="{{ item.uid }}"    group=developers
	loop:
	  - name: joe
	    uid: 1010
	  - name: george
	    uid: 1011
	  - name: ravi
	    uid: 1012
	  - name: kishan
	    uid: 1013
	  - name: kiran 
	    uid: 1014
	  - name: mike
	    uid: 1015
	  - name: tyson
	    uid: 1016
	  - name: shoeb
	    uid: 1017
```
This is how loop works with the array of strings and array of dictionaries.

> Remember that you could simply use items.property name in dictionary to get an item within each dictionary in the list.

> Note: Array of dictionary can also be represented in this json format `-	{ name: jeo, uid: 1010 }`


The loops directive we just saw is used to create simple loops that iterates over a lot items.
There is another way to create loops in playbooks.

---

### With_*

The loop directive newly added in ansible in past we just have this `with_*` directive.

We can write the same playbook as above using `with_item`

```YAML
-
  name: Create users
  hosts: localhost
  tasks: 
  - user: name="{{ item }}"  state=present
    with_items: 
	  - jeo
	  - george
	  - ravi
	  - mani
```
There is no much difference between the two. 


#### Advantages: 

* 	with_items only iterates over list of items
*	we have multiple `with` directives such as:
	*	`with_file` : Iterates over multiple files
	*	`with_url` : To connect with multiple urls.
	* 	`with_mongodb` : Connects to multiple mongodb databases.
	These are just few among many.

* Everything you see after the `with_` string is a lookup plugin.
	*	lookup plugins: Just this of them as custom script that can do some specific task like read files, connect  to url or connect to database or connect database to other service kubernetes.

