## **Aliasing**
This is aliasing: `a` and `b` refer to the same list object.
```python
a = [1,2,3] 
b = a 
a[0] = 2 
print(b) 
```

Because `b = a` makes `b` reference the same list as `a`, changing `a` also changes `b`.

## A. Predict the output

1. What prints?
```python
x = 5
y = x
x = 10
print(y)
```

2. What prints?
```python
a = "5"
b = 10
print(a + str(b))
```

3. What prints?
```python
score = "90" 
print(score * 2)
```    

4. What prints?
```python
school_name = "Khoury College" 
print(school_name[5] + school_name[2])
```

5. What is the type of each?    
```python
mystery = 734_529.678 
x = "False" 
y = False 
z = None
```

## B. Write small programs

6. **Input and conversion:** Ask the user for a test score (0–100). Print the percentile (score / 100).
	- Requirement: handle that `input()` returns a string.

7. Ask the user for **two numbers** (could be decimals). Print:
	- their sum
	- their difference
	- their product    
```python
a = float(input("First number: "))
b = float(input("Second number: "))
print(a + b)
print(a - b)
print(a * b)
```

8. Given a string `s = "data everywhere"`, write code to print:
	- the first character
	- the last character
	- the length of the string
```python
s = "data everywhere"
print(s[0])
print(s[-1])
print(len(s))
```

8. Create a list `scores = [90, 35, 52]`. Write code to:

- add `100` to the end
	`scores.append(100)`

- remove the first element
    `scores.pop(0)`

- print the updated list and its length
    `print(scores) print(len(scores))`

10. Create a dictionary for a student:
	`student = {"name": "Mina", "score": "95"}`

Write code to:
- print the student’s name
- update the score to an integer 98
- add a new key `"passed"` with a boolean value based on score >= 60
```python
print(student["name"])
student["score"] = 98          # make it int
student["passed"] = student["score"] >= 60
print(student)
```

## C. Debug / fix the code

11. This crashes. Fix it **two ways** (hint: conversion vs formatting).
```python
age = input("Age? ")
print("Next year you will be " + (age + 1))
```

```python
age = input("Age? ")
print("Next year you will be " + str(int(age) + 1))
```

12. This doesn’t do what the author thinks. Fix it.   
```python
x = 5
if x = 10:
    print("x is ten")  
```

## D. Mini challenge

14. Ask the user for 3 unique IDs (numbers). Store them in a **set**. Print how many unique IDs were entered.
    
15. Store coordinates as a **tuple** `(lat, lon)` (floats). Print the tuple and the type of each element.
    

If you want, paste your answers (or your code) and I’ll check them and point out any type/variable mistakes.

