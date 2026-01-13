---
date: 2025-01-08
description: flow chart
tags:
  - neu/CS5001
pageorder: 1
link:
  - "[[CS5001Slides1.pdf]]"
  -  [[CS5001Lab1.pdf]]
---
Instructor: Lothar Narins, PhD.
## Machine Language instructions
- transistor: control flow
	- NPN
	- PNP

### Logic
| **Logic Gate** | Function                           |
| -------------- | ---------------------------------- |
| AND            | high output if all inputs are high |
| OR             | high output if any inputs are high |
| NOT            | inverts input signal               |
## **Data Type**
- TEXT
	1. ASCII (7bits)
	2. Unicode (emoji)
- Real Number
- Image Data
	- pixel (RGB)
	- Sound
	- video
	- 3D
	- fonts

## Machine Language Instructions
- *Definition:* are the basic set of instructions that a computer’s CPU understands and executes directly. These instructions are represented in binary code (0s and 1s), which is the native language of a computer's hardware.
- Example: `01000001 00000000`

## ALU (Arithmetic Logic Unit)
- *Definition:* a fundamental component of a computer's central processing unit (CPU) that performs arithmetic and logical operations on data.

## Assembly Language
- *Definition:* a low-level programming language that is closely related to a computer's machine code.
- Example:
```assembly
MOV AX, 5 ; Move the value 5 into register AX 
ADD AX, 3 ; Add 3 to the value in AX
```

## Algorithm
- **Def:** a step-by-step procedure or set of rules used to perform a specific task or solve a particular problem. It is a well-defined sequence of instructions that are executed in order to achieve a desired outcome.

### Flow Charts
![[Pic_Screenshot 2025-01-09 at 10.09.57 AM.png|200]]

## Variable Naming Conversion
- Camel Case
- Pascal Case
	- Eg: `TotalAmountDue`
- *lower_case_with_underscores (Snake Case)
	- Eg: `first_name`, `total_amount_due`
- Uppercase Snake Case

## **Variable Types**
### 1. Numeric Types
- *Integer*(`int`): Whole numbers, both positive and negative.
    - Example: `x = 5`
- *Floating Point* (`float`): Decimal numbers.
    - Example: `y = 3.14`
- Complex (`complex`): Numbers with a real and imaginary part.
    - Example: `z = 2 + 3j`
### 2. Text Type
- String (`str`): A sequence of characters (text).
    - Example: `name = "Alice"`
    - You can use single (`'`) or double (`"`) quotes for strings.
### 3. Sequence Types
- **List (`list`)**: Ordered, mutable collection of items, enclosed in square brackets `[]`.
    - Example: `fruits = ["apple", "banana", "cherry"]`
- **Tuple (`tuple`)**: Ordered, immutable collection of items, enclosed in parentheses `()`.
    - Example: `coordinates = (10, 20)`
- **Range (`range`)**: Immutable sequence of numbers, commonly used in loops.
    - Example: `r = range(5)`

### 4. Mapping Type
- Dictionary (`dict`): Collection of key-value pairs, enclosed in curly braces `{}`.
    - Example: `person = {"name": "Alice", "age": 30}`

### 5. Set Types
- Set (`set`): Unordered collection of unique elements, enclosed in curly braces `{}`.
    - Example: `numbers = {1, 2, 3, 4}`
- Frozen Set (`frozenset`): Immutable version of a set.
    - Example: `frozen_numbers = frozenset([1, 2, 3])`

### 6. Boolean Type
- Boolean (`bool`): Represents `True` or `False` values.
    - Example: `is_active = True`

### 7. Binary Types
- Bytes (`bytes`): Immutable sequence of bytes, often used for binary data.
    - Example: `data = b"hello"`
- Bytearray (`bytearray`): Mutable sequence of bytes.
    - Example: `data_array = bytearray([65, 66, 67])`
- Memory view (`memoryview`): Memory view object for handling large data efficiently.
    - Example: `mem = memoryview(b"hello")`

```python
x = 10          # int
y = 3.14        # float
name = "John"   # str
is_valid = True # bool
fruits = ["apple", "banana"] # list
person = {"name": "John", "age": 25} # dict
```