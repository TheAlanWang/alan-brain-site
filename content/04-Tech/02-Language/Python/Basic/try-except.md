### What data type you’re creating?

```python
string = ""
set1 = set() # 
list1 = [] # square brackets
tuple1 = ()
dic = {} # curly braces
```

mutable vs immutable
Mutable = can be changed _in place_ after it’s created.  
Immutable = cannot be changed after it’s created (you must create a _new_ object instead).

- list: mutable
- set: mutable, No duplicates, Unordered, can add, remove
- tuple: immutable
- dic:

immutable:`int`、`float`、`bool`、`str`、`tuple`
### Structure:
```python
if __name__ == "__main__":
    greet()
```

### try-except
```python
try:
    int("abc")
except Exception as e:
    print(type(e))
```