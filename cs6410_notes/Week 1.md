
Builds on traditional foundational undergrad compiler course

Will expand on foundations where possible

**Focus**: Principles and practice of building efficient implementations of modern programming languages

**Learning Mechanism**: building a simplified compiler

**Course goals**:
- Understand a structure of a modern compiler
- Analyze the major algorithms used to translate code from high-level to machine language

8 total quizzes
- 5 best scores will be taken

6 total homeworks

Project
- Biggest component of the course
- Give a deeper understanding of concepts relevant to modern-day compilers

Useful extra books:
- Computer Systems: A Programmer's Perspective
- Programming Languages Norman Ramsey

The role of a compiler is to understand our intent and enhance our intent

At a high level, a compiler has two pieces:
- Front end - Analysis
	- Read source program and discover its structure and meaning
- Back end - Synthesis
	- Generate equivalent target language program
		- Can be assembly or another higher level programming language

A compiler's goal is to translate source code into target code

The end result of the front end is an **intermediate representation (IR)**
- Typically, but not always, an **abstract syntax tree (AST)**

The intermediate representation is fed into the compiler's backend to generate the target code

A compiler must
- Recognize **legal programs** (and complain about illegal ones)
	- Typically happens on front end
		- During parsing or scanning
- Generate correct code
	- Compiler can attempt to optimize code but cannot change behavior (i.e. meaning)
- Manage runtime storage of all variables/data
- Agree with OS and linker on target format

Phases communicate using some sort of IR
- Front end maps source into IR
- Back end maps IR to target machine code

Often multiple IRs - higher level at first, lower level in later phases

Front end is usually split into two parts:
1. **Scanner**: responsible for converting character stream to token stream: keywords, operators, variables, constants
	- Strips out whitespace
	- Tokens are atomic items, not character strings
	- Tokens may carry associated data (e.g. int value, variable name, etc.)
	- Will check if undefined tokens are used (e.g. non-existent operators or keywords)
		- Checks if something illegal syntax is used
2. **Parser**: reads token stream and generates IR (if possible)
	- Either here or shortly after, perform semantic analysis to check for things like type errors
	- Does semantic checking
		- Is there any meaning in the valid token string that was produced

Scanners and parsers can be generated automatically
- Use a formal grammar to specify the source language
- Tools read the grammar and generate scanner and parser (lex/yacc or flex/bison for C/C++, JFlex/CUP for Java)

Compilers generally just ignore comments









