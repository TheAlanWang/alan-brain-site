---
tags:
  - neu/CS5002
pageorder: 2
date: 2025-01-09
---
Have Homework

Successive divine by 2 till we reach a quotient of 0 and we could copy the remainder in reverse code.
## Operations
### Addition
```md
  101101
+  11010
--------
 1000111
```
### Subtraction
```md
  101
-  11
-----
   10
```

```md
  110101
-  10111
--------
  011110
```
### Multiplication

```md
   1011
x   110
-------
   0000
  1011
 1011
-------
1000010
```
### Division
```md
       100   (Quotient)
     ------
 11 | 1101   (Dividend)
      11     (Divisor × 1)
    ------
       001   (Result after subtraction)
```

we said earlier we can convert decimal -> binary successive division by 2
want to convert from base 3 -> base 5 successive division by 5 (division by base 3 system which we are not used to)

Instead base 3 ------> base 10 -----> base 5
- base 3 -> base 10 (by writing out the expansion in terms of power of 3 )
- base 10 -> base 5 (successive division by 5 in the decimal system)

# Negative number
How to represent signal binary number:
## Signed Magnitude
### Signed Magnitude Convention
> Definition: The **signed magnitude convention** is a method for representing signed numbers in binary form. It uses *the most significant bit (MSB)* as a **sign bit** to indicate whether the number is positive or negative, while the remaining bits represent the magnitude of the number. 

Pros
- easy to figure out positive or negative 
Cons:
- loose range
- double 0

```md
Signed Magnitude 
   (16  8  4  2  1) --- used table easy to cal
+7   0  0  1  1  1
-7   1  0  1  1  1
-------------------(Addition)
     1  1  1  1  0 -> which is 16+8+4+2=30
```
### Signed Complement System
`0000 0000` 8 Bits
`1111 1111`

### Transfer to two's complement for addiction
$x-y=x+(-y)=x+(2^8-y)$

If y = 3, $2^8-3=256-3=253$ convert to binary`1111 1101`
- $3_{(10)}$ in binary is    `0000 0011`
- $-3_{(10)}$ in binary is `1111 1101`
```
n-128 -n5 -n4 -n3 -n2 -n1  n1 n2 n3 n4 n5 n6 ... n127 n-128
      -5  -4  -3  -2   -1  0  1  2  3  4  5 
```


