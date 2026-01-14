---
tags:
  - neu/CS5001
date: 2025-02-19
description: error, set, hash
weight: 7
link:
  - "[[CS5001Slides7.pdf]]"
  - "[[CS5001Lab7.pdf]]"
---
### Errors:
 - AttributeError
 - TypeError
 - ValueError

Before performing a task, check the inputs
if not:
- raise an error
- Or Recollect input

Ask Forgiveness

```python
try:
	possibly
except ValueError()
	handle_value_error(ex)
```

```python
if not instance(width, int):
	raise TypeError("width must be an integer")
```

### File Input/Output

```python
file = open("file.txt", "r")
contents = file.read()
file.close()
```

```python
with open(filename, "r") as file
	file.write()
```

### Sequence Type
Lists, Tuples, Strings
Map sequential integer

#### Associate Arrays
- Map keys to values
- can insert items
- can update values
- can retrieve values
- can remove items

### Hash Function
Maps key data type into integers
should be fast to compute
Example:
```python
def hash(string):
	result = 0
	for c in string:
		result += ord(c)
	return result
```

### Dictionaries
```python
dic{}
dictionary = {"apple":"Apfel",

z"pear":"Birne}
dictionary = dict(apple="Apfel",)
dictionary = dict([,])
```

### Set
set(1) = set(2)
set(1) | set(2)
set(1) - set(2)