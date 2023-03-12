# What is YAML
Ansible playbooks configurations files are written in a particular format called ``YAML``.
Yaml file is used to represent data, in this case configuration data. 
It is a type of key: value pair format.

1. `SYNTAX:`
```YAML
Fruit: Apple
Vegetable: Carrot
Liquid: Water
```
>Remember to keep space after `:` colon while writing values.

2. Array/List:
```YAML
Fruits:
-	Orange
-	Apple
-	Banana

Vegetables:
-	Carrot
-	Cauliflower
-	Tomato
```
> The `-` dash indicates that it is an element of array

3. Dictionary/Map:

###### A dictionary is set of properties group together under an item
```YAML
Banana:
	Calories: 105
	Fat: 0.4 g
	Carbs: 27 g

Grapes:
	Calories: 62
	Fat: 0.3 g
	Carbs: 16 g
```
> Notice the black space before every item, They must have equal number of blank spaces before the properties of a single item. 

4. List of Dictionary:
```YAML
Fruits:
  -	Banana:
		Calories: 105
		Fat: 0.4 g
		Carbs: 27 g
  -	Grape: 
		Calories: 62
		Fat: 0.3 g
		Carbs: 16 g 
```

5. Difference: between list and dictionary

| List | Dictionary |
| ---- | ---------- |
| Lists are ordered colleciton     | Dictionary is an unordered collection |
| Here the order of items matter, the two with same values but not having same order are not equal, they are different.							   | The properties can be defined in any order. As long as vaules matches that two dictionary will be the same. |





