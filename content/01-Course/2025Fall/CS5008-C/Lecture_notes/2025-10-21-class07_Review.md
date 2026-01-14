#### variable
```
fn main() -> int
{
	let a: int = 2
	
}
```

Variable Storage
for each variable, we need a place to store its value, *register*

if the variable is global, put into stack

#### Stack Machine
```
FN main  
PUSH 2  
PUSH 3  
PUSH 4  
MUL  
ADD  
PUSH 5  
SUB  
RETURN  
END_FN
```

Stack Frame

**RSP** stands for **Register Stack Pointer** — it’s a special **CPU register** on x86-64 (64-bit) processors.  
It always points to the **top of the stack** — that is, the current memory address where the next value will be pushed or popped.

*rsp*: is not stable
*rbp*: stable position within the stack frame

```asm
foo:
	push rbp;
	mov rbp, rsp; 
	sub rsp, 24;
	; function body
	mov rsp, rbp;
	pop rbp;
	ret
```

`mov rbp, rsp;`This instruction copies the value of the stack pointer (`rsp`) into the base pointer (`rbp`).


```ams
section .text
    global _start

_start:
    ; Compute fib(4) and use the result as the program's exit code
    mov  rdi, 4
    call fib            ; rax = fib(4)
    mov  rdi, rax       ; exit status = rax & 0xff
    mov  rax, 60        ; syscall: exit
    syscall

; fib(n): input rdi = n; output rax = fib(n)
fib:
    push rbp
    mov  rbp, rsp
    sub  rsp, 16              ; allocate 16 bytes: [rbp-8] for n, [rbp-16] for fib(n-1)

    cmp  rdi, 2               ; if (n < 2) return n
    jl   .base

    mov  [rbp-8], rdi         ; store n in local variable

    ; ---- compute fib(n-1) ----
    mov  rax, [rbp-8]         ; rax = n
    lea  rdi, [rax-1]         ; rdi = n - 1
    call fib                  ; rax = fib(n-1)
    mov  [rbp-16], rax        ; save fib(n-1) on the stack (safe from recursion)

    ; ---- compute fib(n-2) ----
    mov  rax, [rbp-8]         ; rax = n
    lea  rdi, [rax-2]         ; rdi = n - 2
    call fib                  ; rax = fib(n-2)

    ; ---- combine results ----
    add  rax, [rbp-16]        ; rax = fib(n-2) + fib(n-1)
    jmp  .done

.base:
    mov  rax, rdi             ; base case: fib(0)=0, fib(1)=1

.done:
    add  rsp, 16              ; restore stack space
    pop  rbp                  ; restore base pointer
    ret
```
### looking up by Name
- hash table: O(n)
### Scopes
```
let a: int = 2
{
	let b: int = 2
	let c: int = 6
	{
		let d: int = 5
	}
}
```
- Symbol Table

## Hash Table


# Review
Data Type:
- char
- short
- int: 4 bytes(32-bits)
- long: 8 bytes(64-bits)

unsigned

- floate
- double

find size of type

#### Pointer:
- Pointer Arithmetic
```c
int val;
int *pointer = &val;
pointer + 1 //move forward by the size of one int
```



```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *array = malloc(4 * sizeof(int));   // allocate 4 ints
    array[0] = 1; array[1] = 2; array[2] = 3; array[3] = 4;

    // later, you want more space:
    array = realloc(array, 8 * sizeof(int)); // now 8 ints
    array[4] = 5;
    array[5] = 6;

    for (int i = 0; i < 6; i++)
        printf("%d ", array[i]);

    free(array);
    return 0;
}
```

#### Memory:
- Static memory
- The stack
- The heap

```c
struct Person
{
	const char *name;
	int id;
}

struct Person person
{
	.name = "Lothar";
	.id = 1729;
}
person.name = "Bob";
struct Person *indirect = &person;
indirect->id = 1337;
(*indirect).id = 1337;
```

#### Enumerations
```c
enum Token{
	TOKEN_IDENTIFIER = 100,
	TOKEN_INT_LITERAL,
}

enum Token t = TOKEN_IDENTIFIER;

if (t == TOKEN_IDENTIFIER) {
    printf("It's an identifier!\n");
}
```

#### union
```c
union Token_Data
{
	int int_value;
	const char *str_value;
};

struct Token
{
	enum Token_Kind kind;
	union Token_Data data;
}
```

char* b = 8 bytes

```c
#include <stdio.h>

typedef union MyUnion {
    int intValue;
    int c[3];
    char *pValue;
} MyUnion;

int main() {
    printf("%lu", sizeof(MyUnion));
    return 0;
}
```

#### GDB Commands
- layout
- start
- next
- step
- print
- ctrl-L
- quit