Can we have invalid source code but produce functionally compatible and valid target code?

Different programming paradigms:
- Procedural/sequential paradigm
- Event driven paradigm
- Object oriented
- Functional
- etc.

The higher the abstraction/more complex the source language the harder it is to turn it into the target language

Front end split into two parts:
1. Scanner: converts character stream into token stream
	- Keywords, operators, variables, constants
	- 3rd party tools can be used to read the grammar and generate the scanner and parser
		- (e.g. lex/yacc or flex/bison for C/C++, JFlex/CUP for Java)
2. Parser: reads token stream and generates IR
	- Either here or shortly after, perform semantic analysis to check for things like type errors
	- Purpose of IR is to discern syntax and semantics of the program (i.e. structure an dmeaning)

Parser output is the intermediate representation (IR)
- Most common IR is an <b>Abstract Syntax Tree (AST)</b>
	- Represents the essential meaning of the program without syntactic noise
	- Nodes are operations, children are operands
- Many different forms of IR
	- e.g. in object oriented programming languages a symbol table is needed in tandem with the AST

<b>Static Semantic Analysis (SSA)</b>
- During or after parsing, check that the program is legal and collect info for the back end
	- Type checking
	- Check language requirements like proper declarations
	- Preliminary memory allocations

<b>Back End Structure</b>
- Split into two major parts:
	1. Optimization (code improvement)
	2. Target code generation (machine specific)
- Usually walks the AST to generate lower-level intermediate code before optimization
- Often many iterations of optimization and generation steps

Programs can be compiled or interpreted (or sometimes both)
- <b>Compiler</b>: A program that translates a program from one language (the source) to another (the target)
	- Read and analyze entire program
	- Translate to semantically equivalent program in another language
		- Presumably easier or more efficient to execute
	- Offline process
	- Tradeoff: compile time overhead
		- (preprocessing) vs execution performance
- <b>Interpreter</b>: A program that reads a source program and produces the results of executing that program on some input
	- Typically implemented as an "execution engine/machine"
	- Program analysis interleaved with execution
	- Usually requires repeated analysis of individual statements (particularly in loops and functions)
		- Hybrid approaches can avoid some of this overhead
	- Immediate execution, good debugging/interaction

Hybrid approaches
- Compiler generates byte code intermediate language
	- e.g. compile Java source to JVM.class files which are then interpreted
- Interpret byte codes directly or compile some or all byte codes to native code
	- Variation: <b>Just-In-Time compiler (JIT)</b> detect hotspots and compile on the fly to native code

Since the 1960s, the syntax of every significant programming language has been specified by a <b>formal grammar</b>
- Borrowed from the linguistics community (Chomsky)

Formal definition of a grammar: 4-tuple $(N, T, S, P)$
- $N$: set of all variables or non-terminals
- $T$: set of all terminals
- $S \in N$: Starting symbol
- $P$: Productions

Formal Languages and Automata Theory
- <b>Alphabet</b>: A finite set of symbols and characters
- <b>String</b>: A finite, possibly empty sequence of symbols from an alphabet
- <b>Language</b>: A set of strings (possibly empty or infinite)
- Finite specifications of (possibly infinite) languages
	- <b>Automaton</b> - a <b>recognizer</b>; a machine that accepts all strings in a language (and rejects all other strings)
	- <b>Grammar</b> - a <b>generator</b>; a system for producing all strings in the language (and no other strings)
- A particular language may be specified by many different grammars and automata
- A grammar or automaton specifies only one language

<b>Chomsky's Language Hierarchy</b>
- <b>Regular (Type-3)</b> languages are specified by regular expressions/grammars and finite state automata (FSAs)
	- Specs and implementation of scanners
- <b>Context-free (Type-2)</b> languages are specified by context-free grammars and pushdown automata (PDA)
	- Specs and implementation of parsers
	- Considered to be generated languages
- <b>Context-sensitive (Type-1)</b> languages (not too relevant to programming languages)
	- Most natural languages
- <b>Recursively-enumerable (Type-0)</b> languages are specified by general grammars and Turing machines

Programming languages tend to be regular or context-free languages
- Typically modern day languages (with all their complex components) are type-2 languages

A grammar is a set of rules the defines how to produce all valid strings in a language.

<b>Backus-Naur Form (BNF)</b>: A syntax for describing language grammars in terms of transformation <em>rules</em> (or <em>productions</em>), of the form:
```
<symbol> ::= <expression>|<expression>|...|<expression>
```
- <b>Terminal</b>: a fundamental symbol of the language
- <b>Non-terminal</b>: a high-level symbol describing language syntax
	- Can be transformed into other non-terminal or terminal symbol(s) based on the rules of the grammar
- ex: 
```
<s>::=<n> <v>
<n>::=Tamara | Emily | Daniel
<v>::=laughed | cooked | slept
```
- Grammar rules can be defined recursively, so that the expansion of a symbol can contain the same symbol
	- There must also be expressions that expand the symbol into something non-recursive, so that the recursion eventually ends
	- ex: `<adjp>::=<adj> <adjp> | <adj>`

<b>Parsing</b>: Reconstruct the derivation (syntactic structure) of a program

Reasons for separating the scanner and parser:
- Simplicity and separation of concerns
- Efficiency: Scanner recognizes regular expressions - proper subset of context free grammars

Not always possible to cleanly separate the scanner and parser
- Parser would like to know which names are types and which are identifiers but scanner doesn't know how things are declared
- Hacky solution:
	- Either use simpler grammar and disambiguate later, or communicate between scanner and parser

<b>Principle of Longest Match</b>: The scanner should pick the longest possible string to make up the next token if there is a choice
- e.g. in `return maybe != iffy;`, `!=` is one token not two

Most modern languages are free-form
- Layout doesn't matter
- Whitespace separates tokens
- Alternatives:
	- Fortran - line oriented
	- Haskell, Python - indentation and layout can imply grouping

Other confusions:
- In C++ or Java, is `>>` a shift operator or the end of two nested templates or generic classes?













