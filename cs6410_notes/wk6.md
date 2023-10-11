# Bottom-up Parsing

Goal of Parsing: Convert token stream to an abstract syntax tree
- i.e. perform syntax analysis

**Abstract syntax tree (AST)**
- Captures the structural features of the program
- Primary data structure for next phases of compilation

Stream of tokens is always being processed from left to right

**Common orderings**:
- **Top-down**:
	- Start with the root
	- Traverse the parse tree depth-first, left-to-right (leftmost derivation)
	- LL(k), recursive-descent
- **Bottom-up**
	- Start at the leaves (terminals/tokens) and build up to the root
		- Effectively a rightmost derivation in reverse(!)
	- LR(k) and subsets (LALR(k), SLR(k), etc.)
		- If a parser contains R (i.e. R in LR) you know it's bottom up
			- First letter (L) means left-to-right
			- Second letter (R) means rightmost derivation
			- Third letter (k) means how many tokens you are looking ahead
				- Most production parsers look 1 token ahead
				- The higher k the easier it is to parse but the harder it is to generate and generalize your parser

Given what we've already seen and the next input symbol (i.e. the lookahead), choices are:
- Perform a reduction (reduce)
- Look ahead further (shift)
- **shift-reduce parser**

Key data structures
- **Stack** holding the frontier of the tree
- **String** with the remaining input (tokens)

Something to encode the rules that tell us what action to take next given the state of the stack and the lookahead symbol
	- Typically a table encodes a **finite automata**

Shift-reduce parser operations
- **Reduce**: if the top of the stack is the right side of a handle $A ::= \beta$, pop $\beta$ and push $A$
- **Shift**: push the next input symbol onto the stack
- **Accept**: announce success
- **Error**: syntax error discovered

A shift-reduce parser's DFA can be encoded in two tables
- One row for each state
- **action** table encodes what to do given the current state and the next input symbol
- **goto** table encodes the transitions to take after a reduction

Given the current state and input symbol, the main possible actions are:
- $s_i$ - shift the input symbol and state $i$ onto the stack (i.e. shift and move to state $i$)
- $r_j$ - reduce using grammar production $j$
	- The production number tells us how many <symbol, state> pairs to pop off the stack (= number of symbols on rhs of production)

Other possible **action** table entries
- **accept**
- **blank**  - no transition - syntax error
	- A LR parser will detect an error as soon as possible on a left-to-right scan
	- A real compiler needs to produce an error message, recover, and continue parsing when this happens

**LR States**
- Idea is that each state encodes
	- The set of all possible productions that we could be looking at, given the current state of the parse, and
	- Where we are in the right-hand side of each of those productions

If $S$ is the start symbol of grammar $G$, then:
1. If $S =>^* \alpha$ then $\alpha$ is a **sentential form** of $G$
2. $\gamma$ is a **viable prefix** of $G$ if there is some derivation $S =>^*_{rm} \alpha A w =>^* \alpha \beta w$ and $\gamma$ is a prefix of $\alpha \beta$
3. The occurrence of $\beta$ in $\alpha \beta w$ is a **handle** of $\alpha \beta w$
4. An **item** is a marked production (a . at some position in the right hand side) $[ A ::= . X Y ] [ A ::= X . Y ]  [ A ::= X Y . ]$


Grammars can cause problems when constructing a LR parser
- **Shift-reduce conflicts**
	- Use principle of longest match to resolve (i.e. shift)
- **Reduce-reduce conflicts**
	- May want to revisit production rules

SLR (simple LR) parsers
- Uses information about what can follow a non-terminal to decide if we should perform a reduction
- Don't reduce if the next input symbol can't follow the resulting non-terminal
- We need to be able to compute $\text{FOLLOW}(A)$ - the set of symbols that can follow $A$ in any possible derivation
	- i.e. t is in $\text{FOLLOW}(A)$ if any derivation contains $A$t
	- To compute this, we need to compute $\text{FIRST}(\gamma)$ for strings $\gamma$ that can follow $A$

LALR(1) parsers are a variation of LR(1) but merge any two states that differ only in lookahead
- will have the same number of states as SLR parsers, even though LALR(1) is more powerful
- after the merge step, acts like SLR parser with "smarter" $\text{FOLLOW}$ sets (can be specific to particular handles)
- Most practical bottom-up parser tools are LALR(1)
	- e.g. yacc, bison, CUP

# Top-Down Parsing Strategies

General structure:
- Begin at root with start symbol of grammar
- Repeatedly pick a non-terminal and expand
- Success when expanded tree matches input token stream
- Failure when:
	- Reached leaf of tree but it does not match the token stream
		- May be resolved by backtracking
			- backtracking can be very expensive
- LL(k)

Problems:
- Direct left recursion occurs when the left hand non-terminal matches the production rule it is in:
	- e.g. $A = Ab$
	- Unresolvable for top-down parsers

Ideally: only one possible (meaningful) production for $A$

**Predictive Parsing**
- Predictive grammar: if we are located at some non-terminal $A$, and there are two or more possible productions we want to make the correct choice by looking at just the next input symbol
	- If we can do this, we can build a **predictive parser** that can perform a top-down parse without backtracking

**LL(1) Property**: A grammar has this property if for all $A$, if productions $A ::= \alpha$ and $A ::= \beta$ both appear in the grammar, then it is true that $\text{FIRST}(\alpha) \cap \text{FIRST}(\beta) = \emptyset$.
- If a grammar has the LL(1) property, we can build a predictive parser for it that uses 1-symbol lookahead

A main advantage of top-down parsing is that it is easy to implement by hand
- Key idea: write a function corresponding to each non-terminal in the grammar


Real parsers need:
- Better error recovery (don't get stuck on bad tokens)
- Semantic checks (declarations, type checking, etc.)
- Some sort of processing after recognizing (build AST, 1-pass code generation, etc.)

Invariant for Parser Functions
- The parser functions need to agree on where they are in the input
- **Useful invariant**: When a parser function is called, the current token is the token that begins the expanded non-terminal being parsed
	- **Corollary**: when a parser function is done, it must have completely consumed input corresponding to that non-terminal

# Semantics

**Semantic analysis** is considered to be the last step of the front-end of the compiler

There is a level of correctness not captured by a context-free grammar

Main tasks:
- Extract types and other information from the program
- Check language rules that go beyond the context-free grammar
- Resolve names - connect declarations and uses
- "Understand" the program

Key data structure: **Symbol tables**
- For each identifier, record its attributes (kind, type, etc.)
- Later: assign storage locations (stack frame offsets) for variables, add other annotations

Grammar = BNF
- Short: e.g. Java in a handful of pages

Semantics = Language Reference Manual
- Long: e.g. Java SE 8 = 788

For each language construct, we want to know:
- What semantic rules should be checked
- For an expression, what is its type (is it legal?)
- For declarations, what to capture for use elsewhere






