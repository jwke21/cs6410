**x86-64 Data types**
- Integrals - 1, 2, 4, 8 bytes
- Floating point data - 4, 8, 10, 2x8, 4x4, 8x2 bytes
- No aggregate data types such as arrays or structs

**Operand Types**
- **Immediate**: Constant integer data
	- e.g. `0x400`, `-533`
- **Register**: 1 of 16 registers
	- e.g. `rax`, `r13`
- **Memory**: Consecutive bytes of memory at a computed address

3 basic kinds of instructions
1. Transfer data between memory and register
	- Load data from memory into register
		- `reg = Mem[address]`
	- Store register data into memory
		- `Mem[address] = reg`
1. Perform arithmetic operation on register or memory data
2. Control flow: what instruction to execute next

**Stack Frames**
- When a method is called, a **stack frame** is traditionally allocated on logical "top" of the stack to hold its local variables
- By convention, `%rbp` (base pointer) points to a known offset into the stack frame
	- Local variables referenced relative to `%rbp`
	- Base pointer common in 32-bit x86 code; less so in 64-bit where push/pop used less and stack frame has a fixed size so locals can be referenced from `%rsp` easily

If the assembler can't figure out the size of the operands to move, you can explicitly tell it to move 64 bits with the qualifier `qword ptr`
- Intel specific

Control flow - GOTO
- At the assembly level, all we have is `goto` and conditional `goto`
	- Loops and conditional statements are synthesized from these

**Some Optimizations**
- Use left shift to multiply by powers of 2
	- e.g. `8 * x = 2^3 * x = x << 3`
- If you are multiplying by 2 it is faster to decompose into addition
	- i.e. use `x + x` instead of `2 * x`
- Use `decq` for `x - 1`

**Signed Integer Division**
- Horrible on x86-64



