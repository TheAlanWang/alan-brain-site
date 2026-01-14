```
Source code (.jive)
      ↓
lexer.c  →  tokens
      ↓
parser.c →  AST (Abstract Syntax Tree)
      ↓
codegen.c →  output (machine code / IR / pseudo code)
```

| Stage                                       | Description                                         | Input → Output                   | How to Run                                                                               |
| ------------------------------------------- | --------------------------------------------------- | -------------------------------- | ---------------------------------------------------------------------------------------- |
| Lexer<br>`lexer.c` (compiled with `main.c`) | Tokenize `.jive` source into a token list           | `simple.jive` → tokens (printed) | 1. `gcc -Wall main.c -o ../build/jive`<br>2.`../build/jive ../jive_programs/simple.jive` |
| Parsing`parser.c` (included in `main.c`)    | Build an **AST** (Abstract Syntax Tree) from tokens | tokens → AST                     | (Same command as above — it already includes parser when un-commented)                   |
| Code Generation<br>`codegen.c`              | Translate AST → `.asm` assembly text                | AST → `out.asm` (assembly file)  | Inside your program, it writes a `.asm` file like `../build/out.asm`                     |
| Assembling<br>`nasm`                        | Convert assembly into binary machine code           | `.asm` → `.o`                    | `nasm -felf64 out.asm -o out.o`                                                          |
| Linking<br>`ld` (or `gcc`)                  | Combine `.o` files into an executable program       | `.o` → executable                | `bash ld -o out out.o # or gcc out.o -o out`                                             |
| Run your executable<br>`./out`              | Test final result                                   | executable → output              | `bash ./out`                                                                             |


| Stage                  | Input               | Output                     | File in your project |
| ---------------------- | ------------------- | -------------------------- | -------------------- |
| 1. Lexer               | `.jive` source code | tokens                     | `lexer.c`            |
| 2. Parsing             | tokens              | AST (Abstract Syntax Tree) | `parser.c`           |
| **3. Code Generation** | AST                 | Assembly code (`.asm`)     | `codegen.c`          |
| **4. Assembling**      | `.asm`              | machine code (`.o`)        | via `nasm`           |
| **5. Linking**         | `.o`                | executable (`.out`)        | via `ld` or `gcc`    |

这是你“Jive 语言”的编译器入口 `main.c`。它把几个模块（`string.c`, `lexer.c`, `parser.c`, `codegen.c`）**统一编译**（unity build）到一起，然后：
1. 解析命令行参数（确定输入/输出文件名），
2. 调用词法分析（lexer）→ 语法分析（parser）→ 代码生成（codegen）。

**Unity build 是什么？**  
就是把多个 `.c` 文件 `#include` 进同一个翻译单元里编译。
 - 优点：编译时更容易内联、减少链接麻烦；
 - 缺点：编译时间长、命名冲突更容易暴露。  

### build.sh
```zsh

```
### main.c
CLI → read source → lex to tokens → (debug print) → parse to AST → (debug print) → codegen ASM → close → exit.

#### block1:
```c
typedef struct Options
{
	const char *out_file_name;
	const char *in_file_name;
} Options;
```

eg:
```zsh
./jive hello.jive -o result.asm
```

options.in_file_name  = "hello.jive";
options.out_file_name = "result.asm";

#### block 2:
```c
// Print usage information
void print_usage(const char *program_name)
{
	printf("Usage: %s input_file.jive [-o output_file.asm]\n", program_name);
}
```

#### block 3:
```c
/ Main function: Entry point of the compiler
int main(int arg_count, const char **args)
{
	// Parse command line arguments
	Options options = {
		.out_file_name = "../build/out.asm", // Default output file name
		.in_file_name  = NULL,
	};
	
	int arg_index = 0;
	const char *program_name = args[arg_index++];
	
	while (arg_index < arg_count)
	{
		const char *arg = args[arg_index++];
		if (strcmp(arg, "-o") == 0) // Set output file name
		{
			if (arg_index < arg_count)
			{
				options.out_file_name = args[arg_index++];
			}
			else
			{
				printf("ERROR: Missing output file name after -o flag.\n");
				return 1; // Exit with error
			}
		}
		else if (options.in_file_name == NULL) // If no flag, set input file name
		{
			options.in_file_name = arg;
		}
		else
		{
			printf("WARNING: Unrecognized command line argument %s\n", arg);
		}
	}
	
	if (options.in_file_name == NULL)
	{
		printf("ERROR: No input file supplied.\n");
		print_usage(program_name);
		return 1; // Exit with error
	}
	
	//
	// Step 1 of compilation: Lexical Analysis
	//
	
	Token_Array tokens = lex_file(options.in_file_name);
	
	bool test_lexer = true;
	if (test_lexer)
	{
		printf("Lexer output:\n");
		print_token_array(tokens);
	}
	
	//
	// Step 2 of compilation: Parsing tokens into an Abstract Syntax Tree (AST)
	//
	
	Parse_Result parse_result = parse_program(tokens);
	if (!parse_result.success)
	{
		printf("ERROR: Failed to parse.\n");
		return 1; // Exit with error
	}
	
	bool test_parser = true;
	if (test_parser)
	{
		printf("Parser output:\n");
		print_ast(parse_result.ast);
	}
	
	//
	// Step 3 of compilation: Generate asm code by traversing AST
	//
	
	FILE *out_file = fopen(options.out_file_name, "w");
	if (!out_file)
	{
		printf("ERROR: Could not open %s for writing.\n", options.out_file_name);
		return 1; // Exit with error
	}
	
	generate_asm(parse_result.ast, out_file);
	
	fclose(out_file);
	
	return 0; // Exit with success
}
```

##### detail 1 → `**args`
```c
int main(int arg_count, const char **args)
	int arg_index = 0;
	const char *program_name = args[arg_index++];
```
- `arg_count`, `char *argv[]`  → the system handles it for you.
- `char **` → pointer to a pointer → an array of strings.

```
alanwang@Mac test % ./args_test hello.jive -o result.asm
Total arguments: 4
---- Argument list ----
args[0] = ./args_test
args[1] = hello.jive
args[2] = -o
args[3] = result.asm
```

##### detail 2 → `strcmp`
```c
#include <string.h>
while (arg_index < arg_count)
	{
		const char *arg = args[arg_index++];
		if (strcmp(arg, "-o") == 0) // Set output file name
```
- `strcmp` → compare string
- `strcmp` doesn’t “know” the string length beforehand.  It keeps comparing one character at a time until it reaches the special `'\0'` end marker./

##### detail 3 → `lex_file`
```c
#include <lexer.c>
	Token_Array tokens = lex_file(options.in_file_name);
	
	bool test_lexer = true;
	if (test_lexer)
	{
		printf("Lexer output:\n");
		print_token_array(tokens);
	}
```
`Token_Array tokens = lex_file(options.in_file_name);`
- Q: `#include <lexer.c>` means ?
	- the **C preprocessor** literally copies the entire contents of `lexer.c`  and pastes it into your current file (here, `main.c`) before compilation.

```c
Token_Array lex_file(const char *file_name)
{
    // 1. Open the source file
    // 2. Read it character by character
    // 3. Recognize keywords, identifiers, symbols, numbers, etc.
    // 4. Create and return a Token_Array containing all tokens
}
```

### gcc main & `lexer.c`

```zsh
# method 1
gcc lexer.c -o ../build/lexer
../build/lexer ../jive_programs/simple.jive

# method 2 main 
gcc main.c -o ../build/jive

../build/main ../jive_programs/simple.jive
```

Because `main.c` called `lexer.c`, only need to gcc `main.c`
gcc →  GNU Compiler Collection
```c
gcc -std=c11 -Wall -Wextra main.c -o ../build/jive
gcc main.c -o ../build/jive
../build/jive ../jive_programs/simple.jive
```

### `lexer.c`
```c
typedef enum Token_Kind
{
	TOKEN_KEYWORD,
	TOKEN_IDENT,
	TOKEN_OPEN_PAREN,
	TOKEN_CLOSE_PAREN,
	TOKEN_ARROW,
	TOKEN_TYPE,
	TOKEN_OPEN_BRACE,
	TOKEN_CLOSE_BRACE,
	TOKEN_INTEGER,
	TOKEN_STRING,
} Token_Kind;
```

```c
typedef struct Token
{
	Token_Kind kind;
	String source; // stores lexeme text (e.g., ident name or raw literal)
	Loc loc;
	union
	{
		long long_value; // for TOKEN_INTEGER
		Keyword keyword; // for TOKEN_KEYWORD
		Type type;		 // for TOKEN_TYPE
	};
} Token;
```

##### detail → token
```c
typedef struct Token
{
	Token_Kind kind;
	String source; // stores lexeme text (e.g., ident name or raw literal)
	Loc loc;
	union
	{
		long long_value; // for TOKEN_INTEGER
		Keyword keyword; // for TOKEN_KEYWORD
		Type type;		 // for TOKEN_TYPE
	};
} Token;
```
##### detail → convert an enum to a string
convert an enum to a string
```c
static const char *kw_name(Keyword k)
{
	switch (k)
	{
	case KEYWORD_FN:
		return "fn";
	case KEYWORD_RETURN:
		return "return";
	default:
		return "?";
	}
}
static const char *type_name(Type t)
{
	switch (t)
	{
	case TYPE_INT:
		return "int";
	case TYPE_BOOL:
		return "bool";
	default:
		return "?";
	}
}

// for printing token kind column
static const char *kind_display(Token_Kind k)
{
	switch (k)
	{
	case TOKEN_KEYWORD:
		return "KEYWORD     ";
	case TOKEN_IDENT:
		return "IDENTIFIER  ";
	case TOKEN_OPEN_PAREN:
		return "(";
	case TOKEN_CLOSE_PAREN:
		return ")";
	case TOKEN_ARROW:
		return "->";
	case TOKEN_TYPE:
		return "TYPE        ";
	case TOKEN_OPEN_BRACE:
		return "{";
	case TOKEN_CLOSE_BRACE:
		return "}";
	case TOKEN_INTEGER:
		return "INTEGER     ";
	case TOKEN_STRING:
		return "STRING      ";
	case TOKEN_EOF:
		return "EOF";
	default:
		return "?";
	}
}
```

##### detail → `print_token_array`
```c
void print_token_array(Token_Array arr)
{
	for (long i = 0; i < arr.count; i++)
	{
		Token *t = &arr.items[i]; // pointer to current token
		printf("%s:%ld:%ld    %s",
			   t->loc.file_name ? t->loc.file_name : "<input>",
			   t->loc.line, t->loc.col,
			   kind_display(t->kind)); // 

		if (t->kind == TOKEN_KEYWORD)
		{
			printf(" %s", kw_name(t->keyword));
		}
		else if (t->kind == TOKEN_TYPE)
		{
			printf(" %s", type_name(t->type));
		}
		else if (t->kind == TOKEN_IDENT)
		{
			if (t->source.data && t->source.count > 0)
				printf(" %s", t->source.data);
		}
		else if (t->kind == TOKEN_INTEGER)
		{
			printf(" %ld", t->long_value);
		}
		// TOKEN_EOF: no extra payload
		printf("\n");
	}
}

```


### gcc main & `lexer.c` & `parser.c`

```zsh
# method 1
gcc lexer.c -o ../build/lexer
../build/lexer ../jive_programs/simple.jive

# method 2 main 
gcc main.c -o ../build/jive

../build/main ../jive_programs/simple.jive
```

### `parser.c`
##### detail  → `fprintf`
```c
// kind_display(Token_Kind) is provided by lexer.c
static void print_token_kind(Token_Kind kind)
{
	fprintf(stderr, "%s", kind_display(kind));
}
```