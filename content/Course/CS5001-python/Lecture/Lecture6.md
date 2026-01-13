---
date: 2025-02-12
pageorder: 6
description: quiz
tags:
  - neu/CS5001
link:
  - "[[CS5001Slides6.pdf]]"
  - "[[CS5001Lab6.pdf]]"
---
## Slice
```python
items[ : : ]
```


```python
def my_function(*args): 
	print(args) my_function(1, 2, 3, "hello")
```

### error
```python
try:
except AttributeError:
	print()
	
try:
except InputError:

ValueError # int("Hello")
```


```python
def computer_area(width, int):
	return width * height
```

```python
if not instance(width, int)
```

Ask permission:


Asking forgiveness:
when errors are unpredictable, try to run the code, and catch exceptions as they occur.
```python
try:
	possibly
except:
	handle_error()
```

To handle specific errors:
```python
except ValueError as ex:
	hanel_value_error(ex)
```

```python
except (ValueError, TypeError)as ex:
	handle_value_error(ex)

while True: 
	try: user_input = input("Enter a number: ") 
	num = int(user_input) # Trying to convert input to an integer print(f"You entered: {num}") 
	break # Exit loop if successful 
except ValueError: 
	print("Wrong input! Please enter a valid number.")

