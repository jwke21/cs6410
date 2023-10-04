# Parser

A parser reads a token stream and generates an IR

Scanning relies on automaton to generate tokens
- Most programming languages are regular languages meaning that they can be represented using FSA

We prefer our scanners to be DFAs over NFAs because DFAs allow backtracking

**From DFA To NFA**
- **Subset construction**: construct a DFA from the NFA, where each DFA state represents a set of states in the NFA
	- Complexity: construction of the set of DFA states, $D$, from the NFA states, $N$, and the derivation of $\delta_{DFA}$
	- Solution: Hopcroft's Algorithm (DFA to Minimal DFA)

**Fixed-Point Computation**: Computation characterized by an iterated application of a **monotone function** to some set of sets drawn from a known domain
- Some function $f$ is **monotone** if: $$\forall x,y \in D(f), x \leq y \rightarrow f(x) \leq f(y)$$
- Computation terminates when it reaches a state where further iteration produces the same answer - a "**fixed point**" in the space of successive iterations

**Subset Construction Algorithm**:
```
Input: an NFA
Output: an equivalent (in acceptance) DFA

1. Create state table from the given NFA.
2. Create a blank slate table under possible input. alphabets for the equivalent DFA.
3. Mark the start state of the DFA the same as NFA, q_0.
4. For each possible input, find the combination of states that can be reached from the current state. Those states form a new DFA states, {Q0, Q1, ..., Qn}.
5. Every time a new DFA state is generated under the input alphabet columns, repeat step 4 again, otherwise go to step 6.
6. The states which contain any of the final states of the NFA are the final states of the equivalent DFA.
```


A scanner is a DFA that finds the next token each time it is called

Tokens are the internal compiler names for the lexemes

**From DFA to Code**
- **Option 1**: implement by hand using procedures
	- **Option 1a**: like option a, but structured as a single procedure with multiple return points
- **Option 2**: use a tool to generate table driven scanner
	- Rows: states of DFA
	- Columns: input characters
	- Entries: action
		- Go to next state
		- Accept token, go to start state
		- Error
	- **Option 2a**: use tool to generate scanner

**Principle of Longest Match**: In most languages, the scanner should pick the longest possible string to make up the next token if there is a choice

**Syntactic Analysis/Parsing**:
- Goal: Convert token stream to an abstract syntax tree
- Abstract Syntax Tree (AST)
	- Captures the structural features of the program
	- Primary data structure for next phases of compilation
- Plan
	- Study how context-free grammars specify syntax
	- Study algorithms for parsing and building ASTs

The syntax of most programming languages can be specified by a **context-free grammar (CFG)**
- Are a sweet spot
	- Powerful enough to describe nesting, recursion
	- Easy to parse
		- Restrictions on general CFGs improve speed
- Not perfect
	- Cannot capture semantics (e.g. "must declare every variable")
		- Requires later semantic pass
	- Can be ambiguous

**Derivations and Parse Trees**
- **Derivation**: a sequence of expansion steps, beginning with a start symbol and leading to a sequence of terminals
- **Parsing**: inverse of derivation
	- Given a sequence of terminals (aka tokens), recover (discover) the non-terminals and structure, i.e. the parse tree (concrete syntax)

**Parsing**: Given a grammar $G$ and a sentence $w$ in $L(G)$, traverse the derivation (parse tree) for $w$ in some **standard order** and do something useful at each node
- Tree might not be produced explicitly, but the control flow of the parser will correspond to a traversal
- **standard order**: we want the parser to be deterministic and we want to examine the source program from left to right

**Common Orderings**
- **Top-down**:
	- Start with the root
	- Traverse the parse tree depth-first, left-to-right (leftmost derivation)/inorder
	- LL(k), recursive-descent
- **Bottom-up**
	- Start at leaves and build up to the root
		- Effectively a rightmost derivation in reverse
	- LR(k) and subsets (LALR(k), SLR(k), etc.)

Formally, a grammar $G$ is a tuple $<N, \Sigma, P, S>$ where:
- $N$ is a finite set of **non-terminal** symbols
- $\Sigma$ is a finite set of **terminal** symbols (alphabet)
- $P$ is a finite set of **productions**
	- A subset of $N \times(N \cup \Sigma)^*$ (Cartesian product)
- $S$ is the **start symbol**, a distinguished element of $N$
	- If not specified otherwise, this is usually assumed to be the non-terminal on the left of the first production

**Derivation relations**
- $\alpha A \gamma => \alpha \beta \gamma$ iff $A ::= \beta$ in $P$
	- Derives
- $A =>^* \alpha$ if there is a chain of productions starting with $A$ that generates $\alpha$
	- transitive closure
- $w A \gamma =>_{lm} w \beta \gamma$ iff $A ::= \beta$ in $P$
	- Derives leftmost
- $\alpha A w =>_{rm} \alpha \beta w$ iff $A ::= \beta$ in $P$
	- Derives rightmost
- We are only interested in leftmost and rightmost derivations - not random orderings

Grammar $G$ is **reduced** iff for every production $A ::= \alpha$ in $G$ there is a derivation: $$S =>^* x A z => x \alpha z =>^* xyz$$
- i.e. no production is useless
- Where $\alpha$ is some production rule

Grammar $G$ is unambiguous iff every $w$ in $L(G)$ has a unique leftmost or rightmost derivation
- Fact: unique leftmost implies unique rightmost and vice-versa

A grammar without this property is **ambiguous** (extremely difficult and often impossible to parse)

Example ambiguous grammar:
```
expr ::= expr + expr | expr - expr | expr * expr | expr / expr | int
int ::= 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9
```
- The grammar has no inherent notion of **precedence** or **associativity**
- Traditional solution:
	- Create a non-terminal for each level of precedence
	- Isolate the corresponding part of the grammar
	- Force the parser to recognize higher precedence sub-expressions first
	- Use left- or right-recursion for left- or right-associative operators (non-associative operators are not recursive)

Example unambiguous grammar:
```
expr ::= expr + term | expr - term | term
term ::= term * factor | term / factor | factor
factor ::= int | ( expr )
int ::= 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
```

Dangling else example of ambiguous gramar:
```
stmt ::= if ( expr ) stmt | if ( expr ) stmt else stmt
```








