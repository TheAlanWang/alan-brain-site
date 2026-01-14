---
date: 2025-02-26
tags:
  - neu/CS5001
weight: 8
link:
  - "[[CS5001Slides8.pdf]]"
  - "[[CS5001Lab9.pdf]]"
  - "[[CS5001Lab10.pdf]]"
description: class&objective
mainpage:
  - "[[DV_Course]]"
---
### Class:
	strings, lists, tuples, dictionaries
- 32 bit (UTF-32)
```python
"1 + 2 * 3".split()
" ".joint(["Lothat","Narins"])
```

#### Design your own data types
An object is a thing created according to blueprint

Abstract: 
1. Vector-A **vector** is a mathematical object that has both **magnitude (size)** and **direction**.
2. Matrix 
3. Rectangle
Data: 1. Image 2.Sound 3.File
Record: 1.Book 2.Movie 3.Employee
Entity: 1.player

### Constructing and using an Object
```python
class Point:
    '''
    represents a location in 2D space.
    Has an x and y coordinate
    '''
    # Definition omitted
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def distance(self, point):
        return ((self.x - point.x)**2 + (self.y-point.y)**2)** 0.5        

p = Point(3, 4)
print(p.x, p.y)
q = Point(7, 7)
print(p.distance(q))
```

### Printing an Object
```python
print(object)
```
#### Printing an Object address
`__init__` used to initialize
`__str__` convert an object to a string
`__eq__` compare objects for equality
`__add__`
`__mul__`
`__rmul__`

```python
class Point:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
    
    def __str__(self):
        return f"Point({self.x},{self.y})"
    
    def distance(self, point):
        return (((self.x - point.x)**2 + (self.y - point.y)**2) ** .5)

    def __add__(self, point):
        return Point(self.x + point.x, self.y + point.y)
    
    def __mul__(self, scalar):
        if not isinstance(scalar, int) and not isinstance(scalar, float):
            raise TypeError("Can only scale by ints or floats")
        return Point(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):
        if not isinstance(scalar, int) and not isinstance(scalar, float):
            raise TypeError("Can only scale by ints or floats")
        return Point(scalar * self.x, scalar * self.y)

    def __getitem__(self, index):
        if index == 0:
            return self.x
        if index == 1:
            return self.y

    def __setitem__(self, index, value): 
        if index == 0:
            self.x = value  
        elif index == 1:
            self.y = value
        
p = Point(3, 4)
q = Point(7, 7)
print(p.distance(q))
print(p)
print(p + q)
print(p * 2)
print(2 * p)
print(f"p[1]={p[1]}")
p[1] = 3
print(p[1])
```

### Inheritance
```python
class Heir(BaseClass):
    #
```

### Testing
python's unitest module
Create a class inheriting from unittest.TestCase

```python
import unittest
from point import Point

class PointTester(unittest.TestCase):
    def test_init(self):
        p = Point(1, 2)
        self.assertEqual(p.x, 1)
        self.assertEqual(p.y, 2)

        q = Point(3, -2)
        self.assertEqual(q.x, 3)
        self.assertEqual(q.y, -2)

def main():
    unittest.main()

if __name__ == "__main__":
    main()
```