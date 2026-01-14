---
date: 2025-01-22
tags:
  - neu/CS5001
description: stack
weight: 3
link:
  - "[[CS5001Slides3.pdf]]"
  - "[[CS5001Lab3.pdf]]"
---
### Stack
Last in First out
- push data onto the top
- pop data off the top
- peek at the to item

### Reverse Polish Notation (RPN)
RPN is easy to evaluate using a **stack-based algorithm**.
- Infix notation: 1 + 2
- RPN 1 2 +

- Infix notation: 1 + 2 * 3
- RPN 1 2 3 * +

```python
expr = "3 4 1 - * 6 3 - /"
tokens = expr.split(" ")
print(tokens)

stack = []

for token in tokens:
	if token.isnumeric():
		value = float(token)
		stack.append(value)
	elif token == '+':
		b = stack.pop()
		a = stack.pop()
		result = a + b
		stack.append(result)
	elif token == '-':
		b = stack.pop()
		a = stack.pop()
		result = a - b
		stack.append(result)
	elif token == '*':
		b = stack.pop()
		a = stack.pop()
		result = a * b
		stack.append(result)
	elif token == '/':
		b = stack.pop()
		a = stack.pop()
		result = a / b
		stack.append(result)
	else:
		print("ERROR:unrecognized token", token)
		break	
	print(stack)

if len(stack) == 1:
	print("Result", stack[0])
else:
	print("Error: Invalid")
```

### Functions
`number_count = len(numbers)`
return value = name + Arguments

#### def a Function
```python
def foo(para):
'''
documentation describing the function
'''
	# body of statements
```

`help(foo)`

#### parameters vs Arguments
```python
def lerp(a, t, b) # parameters
	return

breaktime = lerp(start, 0.5, end) # Arguments
```

```python
def lerp(a: float, t: float, b: float):
	return (1-t) * a + t * b

# although ':', it dont depends on the :
```

### Under the Hood
how to functions know where to return to?
- they use **THE** Stack

Last in First out

### Local Variables
Variables defined inside of a function don't exist outside that function
```python
def foo(x):
	a = x * x
	return a * (a + 2)
```

```python
def bar(x, y):
	a = x * x
	b = foo(y)
	return a + b - x - y
```

### Active Records
the execution stack stores the return address, the local variables

### Variable Scope
```python
a = 1

def foo():
	print(a)

def bar(x):
	a = 2
	return a * x
	
print(a)
```

```python
# actually there is the way could use global A
global a 
```

### Return Statements
The return keyword can be used inside of a function definition

### Return vs. Print
- return is for the computer
- print is for user

### Importing Functions
- can call built-in functions and functions defined in the same files
- call a function in another file or a library
	- from module *import* function
this executes all loose code

To prevent code from running when a module is imported, write all loose code under this conditional:

### Using a Guard
```python
if __name__ == "__main__":
	# Loose code goes here
```

**Loose code** refers to code that is written outside of any structured block.

In fact, all of your loose code should go in a main() function, and the call to main should go under guard:
```python
def main():
	# Code goes here
if __name__ == "__main__":
	# Loose code goes here
	main()
```

```python
# function
def foo():
	print("Hello")
def bar():
	print("Goodbye")  

# loose code: without protect, will execute
print(__name__)

if __name__ == "__main__":
	foo()
	bar()
```

```python
# test loose.py
from function import foo, bar
foo()
bar()
```

### function path
```shell
pip show numpy
```

### Testing Your functions
```python

```