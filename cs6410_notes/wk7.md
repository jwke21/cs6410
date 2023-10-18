Questions:
1. May I list you as reference?
	- If yes: they ask for an email and phone number. But I am happy to just give them your email if that's what you're comfortable with.
	- If no: no problem.
	- `2062280238`
1. I recently saw a video that compared the performance of virtual functions in C++ (equivalent to abstract functions in Java) with a non-virtual function implementation. This showed that the implementation that used virtual functions was much much slower than the implementation without virtual functions. Why is it so much slower?  
2. Can you go over one more time what is meant by a rightmost vs leftmost derivation
	- Given a production rule, it is the order in which you derive the the tokens (i.e. from left->right in leftmost vs. right->left in rightmost)

**Semantic Analysis** key data structure: **Symbol tables**
- For each identifier in the program, record its attributes (kind, type, etc.)
- Later: assign storage locations (stack frame offsets) for variables, add other annotations

**Semantic Checks**
- **Appearance of a name**: id
	- **Check**: id has been declared and is in scope
	- **Compute**: Inferred type of id is its declared type
- **Constant**: v
	- **Compute**: Inferred type and value are explicit
- **Binary operator**: exp$_1$ op exp$_2$
	- **Check**: exp$_1$ and exp$_2$ have compatible types:
		- Identical or well-defined conversion to appropriate types
	- **Compute**: Inferred type is a function of the operator and operand types
- **Assignment**: exp$_1$ = exp$_2$ 
	- **Check**: exp$_1$ is assignable (not a constant or exp)
	- **Check**: exp$_1$ and exp$_2$ have assignment-compatible types
	- **Compute**: Inferred type is type of exp$_1$ 
- **Cast**: (exp$_1$) exp$_2$
	- **Check**: exp$_1$ is a type
	- **Check**: exp$_2$ either
		- Has the same type as exp$_1$ 
		- Can be converted to type exp$_1$ (e.g. double to ing)
		- **Downcast**: is a superclass of exp$_1$ (usually requires a runtime check to verify; at compile time we can decide if it could be true)
		- **Upcast (trivial)**: is the same or a subclass of exp$_1$ 
- **Field reference**: exp.f
	- **Check**: exp is a reference type
	- **Check**: The class of exp has a field named f
	- **Compute**: Inferred type is declared type of f
- **Method call**: exp.m(e$_1$, e$_2$, ..., e$_n$)
	- **Check**: exp is a reference type (class instance)
	- **Check**: The class of exp has a method named m
	- **Check**: The method exp.m has n parameters
		- If overloading allowed, at least one version of m exists with n parameters
	- **Check**: Each argument has a type that can be assigned to the associated parameter
		- Same "assignment compatible" check for assignment
		- Overloading: need to find a "best match" among available methods if more than one is compatible - or reject if result is ambiguous
	- **Compute**: Inferred type is given by method declaration (or could be void)
- **Return statement**: return exp; or: return;
	- **Check**:
		- If the method is not void: The expression can be assigned to a variable with the declared return type of the method
		- If the method is void: There is no expression

**Attribute Grammars**: A systematic way to think about semantic analysis
- Formalize properties checked and computed during semantic analysis and relate them to grammar productions in the CFG (or AST)
- Can be used directly or as a way to organize the semantic analysis you want to perform
- Idea: associate attributes with each node in the AST
	- Example attributes:
		- Type information
		- Storage location
		- Assignable
		- Value
- Notation: X.a if a is an attribute of node X

Given a production X ::= Y$_1$ Y$_2$ ... Y$_n$
- A **synthesized** attribute X.a is a function of some combination of the attributes of Y$_i$'s (bottom up)
- An **inherited** attribute Y$_i$.b is a function of some combination of attributes X.a and other Y$_j$.c (top down)
	- Often restricted: only Y's to the left can be used

In practice, Attribute grammars give us a good way of thinking about how to structure semantic checks
- Symbol tables will hold environment information
- Add fields to AST nodes to refer to appropriate attributes

**Symbol Tables**:
- Map identifiers to <type, kind, location, other properties>
- Operations
	- Lookup(id) => information
	- Enter(id, information)
	- Open/close scopes
- Build & use during semantics pass
	- Build first from declarations
	- Then use to check semantic rules
- Use (and augment) in later compiler phases

**Symbol tables for MiniJava**
- **Global** - Per program information
	- Maps class names to per-class symbol tables
- One symbol table for each class
	- One entry per method/field declared in the class
		- Contents: type information, public/private, parameter types, storage locations, etc.
	- Reached from global table of class names
	- In Java, we actually need multiple symbol tables (or more complex symbol table) per class
		- The same identifier can be used for both a method name and a field name in a single class
- All global tables persist throughout compilation
	- Symbolic information in Java .class or MSIL files
	- Debug info in .o and .exe files
	- Some or all info in library files (.a, .so)
- One local symbol table for each method
	- One entry for each local variable or parameter

**Error Recovery**: what to do when an undeclared identifier is encountered?
- Only complain once
- Can forge a symbol table entry for id once you've complained so it will be found in the future
- Assign the forged entry a type of "unknown"
- "Unknown" is the type of all malformed expressions and is compatible with all other types

**Types**: Classical roles of types in programming languages
- Run-time safety
- Compile-time error detection
- Improved expressiveness (e.g. method or operator overloading)
- Provide information to optimizer

**Type Systems**
- **Base Types**
	- Fundamental, atomic types
		- Can typically access the memory location of these types and can do so within one cycle
	- Typical examples: int, double, char bool
- **Compound/Constructed Types**
	- Built up from other types (recursively)
	- Constructors include records/structs/classes, arrays, pointers, enumerations, functions, modules, etc.

**Types vs ASTs**
- Type nodes are not AST nodes
- **AST** = abstract representation of source program
- **Types** = abstract representation of type semantics for type checking, inference, etc.

**Base Types**
- For each base type create exactly one object to represent it (singleton pattern!)
- Useful to create a type "void" object to tag functions that do not return a value
- Also useful to create a type "unknown" object for errors

**Compound Types**
- Basic idea: use an appropriate "type constructor" object that refers to the component types
	- Limited number of these - correspond directly to type constructors in the language (pointer, array, record/struct/class, function)

**Type Equivalence for Compound Types**
- Two strategies:
	1. **Structural equivalence**: two types are the same if they are the same kind of type and their component types are equivalent, recursively
	2. **Name equivalence**: two types are the same if they have the same name, even if their structures match

