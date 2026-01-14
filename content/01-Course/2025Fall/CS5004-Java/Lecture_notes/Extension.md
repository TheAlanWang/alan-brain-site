In Java, the rule is:
- **Each `.java` file can have at most one `public` class.**
- If there is a `public` class, the **file name must match that class name** (case-sensitive).

## Hello World
```java
class Main {
	public static void main(String[] args){
		System.out.println("Hello world");
	}
}
```
- every statement must end with a semicolon `;`
- pulic: access modifier, the method can be accessed from anywhere
- static: JVM don't need to create an object
- void: return type, don't return any value

declaration -> initialization -> assignment


### Naming:
| Item            | Convention                | Example               |
| --------------- | ------------------------- | --------------------- |
| Class/Interface | PascalCase (noun)         | `BankAccount`         |
| Method          | camelCase (verb)          | `getBalance()`        |
| Variable        | camelCase (descriptive)   | `accountBalance`      |
| Constant        | ALL_CAPS_WITH_UNDERSCORES | `MAX_VALUE`           |
| Package         | lowercase                 | `com.example.project` |
| File name       | Matches public class name | `Main.java`           |

### Default Values of Static Variables

| Data Type      | Default Value               |
| -------------- | --------------------------- |
| `byte`         | `0`                         |
| `short`        | `0`                         |
| `int`          | `0`                         |
| `long`         | `0L`                        |
| `float`        | `0.0f`                      |
| `double`       | `0.0d`                      |
| `char`         | `'\u0000'` (null character) |
| `boolean`      | `false`                     |
| Reference type | `null`                      |

### Primitive types and Reference types
- Primitive Type

| Type      | Size   | Example Values  |
| --------- | ------ | --------------- |
| `byte`    | 8-bit  | `1`, `-100`     |
| `short`   | 16-bit | `1000`          |
| `int`     | 32-bit | `42`, `-2000`   |
| `long`    | 64-bit | `100000L`       |
| `float`   | 32-bit | `3.14f`         |
| `double`  | 64-bit | `2.71828`       |
| `char`    | 16-bit | `'A'`           |
| `boolean` | 1-bit* | `true`, `false` |
- Reference Types
	- Any **object** created from a class (including arrays, Strings).
	- Store a **reference (memory address)** that points to the actual object in the heap.
	- Can be `null`.

### Type casting
1. Widening Conversion (Implicit Casting)
2. Narrowing Conversion (Explicit Casting)
	- `int i = (int) d;   // double → int`
3. Converting Between Primitives and Strings
	- `String s = String.valueOf(n);`

