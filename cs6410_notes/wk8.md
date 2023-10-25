# Intermediate Representations (IR)

**Structure**
- Graphical (trees, graphs, etc.)
- Linear (code for some abstract machine)
- Hybrids are common (e.g., control-flow graphs whose nodes are basic blocks of linear code)

**Key design decision**: how much detail to expose
- Structural (graphical) IRs are typically high-level
 Linear IRs are typically low-level (easier for generating target code)

**DAGs**
- Variation on ASTs with **shared substructures**
- **Pro**: saves space, exposes redundant sub-expressions
- **Con**: less flexibility if part needs to be changed

**Linear IRs**
- Pseudo-code for some abstract machine
- Level of abstraction varies
- Simple, compact data structures
- e.g. 
	- 3-address code
	- Stack machine code

**sp**: stack pointer
- Typically points to the top of the stack

**fp**: frame (base) pointer 
- Points to the context that you're in

**Abstraction level tradeoffs**
- High-level:
	- Good for source-level optimizations and semantic checking
	- Can't optimize things that are hidden
- Medium-level:
	- More 
	- Many (all?) optimizing compilers work at this level
- Low-level abstraction:
	- 

**Three-Address Code (TAC)**
- Usual form: $x \leftarrow y \text{op} z$
	- One operator
	- Maximum of 3 names
- e.g. $x = 2 * (m + n)$ becomes:
	- $t1 \leftarrow m + n;$ $t2 \leftarrow 2 * t1;$ $x \leftarrow t2$

**Control Flow Graph (CFG)**


**x86-64**
- 16 64-bit general registers
- 64-bit address space
- Pointers are 8 bytes
- 16 SSE registers

**Two main assembler languages for x86-64**:
1. Intel/Microsoft assembler
2. AT&T/GNU assembler

Differences:
- **Operand order**: `op a, b`
	- Intel/Microsoft: `a = a op b` (dst first)
	- AT&T/GNU: `b = b op a` (dst last)
- **Memory address**:
	- Intel/Microsoft: `[baseregister + offset]`
	- AT&T/GNU: `offset(baseregister)`
- **Instruction mnemonics**:
	- Intel/Microsoft: `mov, add, push, ...`
	- AT&T/GNU: `movq, addq, pushq [operand size is added to end]`
- **Register names**:
	- Intel/Microsoft: `rax, rbx, rbq, rsp, ...`
	- AT&T/GNU: `%rax, %rbx, %rbp, %rsp, ...`
- **Constants**:
	- Intel/Microsoft: `17, 42`
	- AT&T/GNU: `$17, $42`
- **Comments**:
	- Intel/Microsoft: `; to end of line`
	- AT&T/GNU: `# to end of line /* ... */`

Data should normally be aligned on "natural" boundaries for performance, although unaligned accesses are generally supported - but with big performance penalty

Operand types:
- **Immediate**: Constant integer data
- **Register**: 1 of 16 integer registers
	- But exclude `%rsi, %rsp, %rbp`
- **Memory**: Consecutive byes of memory at a computed address

**Three basic kinds of instructions**:
1. Transfer data between memory and register
	- Load data from memory into register
		- `reg = Mem[address]`
	- Store register data into memory
		- `Mem[address] = reg`
2. Perform arithmetic operation on register or memory data
3. Control flow: what instruction to execute next



