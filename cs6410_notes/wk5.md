# Bottom-Up Parsing

**Goal**: Perform syntax analysis of the token stream and build the IR that is then passed to the backend
- IR is most frequently as a AST

**Abstract Syntax Tree (AST)**
- Captures the structural features of the program

**Productions** always have a non-terminal on the left hand side and some combination of non-terminals and terminals on the right hand side of the production rule

The goal is to create some combination of terminals s.t. that combination of terminals can be derived from the production rules
- Derives: $$\alpha A \gamma \rightarrow \alpha \beta \gamma \iff A ::= \beta \in P$$
- Transitive closure: $A \rightarrow ^* \alpha$ if there is a chain of productions starting with $A$ that generates $\alpha$

Leftmost derivation: $\omega A \gamma \rightarrow_{lm} \omega \beta \gamma \iff A ::= \beta \in P$
- Always focus on the left terminal and transform it

Rightmost derivation: $\alpha A \omega \rightarrow_{rm} \alpha \beta \omega \iff A ::= \beta \in P$
- Always focus on right most terminal

**Sentential form**: Combination of terminals and non-terminals that is derived by following the production rules while you are trying to parse the token stream
- Intermediary stage between token stream and IR

Grammar $G$ is **unambiguous** iff every $w$ in $L(G)$ has a unique leftmost (or rightmost) derivation
- Unique leftmost or rightmost implies the other
- A grammar without this property is **ambiguous**
- A requirement to make the grammar parsable

Example things that lead to ambiguities:
- Binary operations: is ambiguous without using the math rules of **associativity of operations** and **precedence** (there is no inherent precedence in grammars, there is only the order in which you apply production rules)

**Top-down**
- left to right
- Leftmost derivation

**Bottom-up**
- left to right
- Rightmost derivation

Idea of bottom-up parsing: read the input token stream left to right
- Whenever we've matched the right hand side of a production, reduce it to the appropriate non-terminal and add that non-terminal to the parse tree

**Frontier**: The upper edge of this partial parse tree

The bottom-up parser reconstructs a **reverse rightmost derivation**
- Given the rightmost derivation $S \rightarrow \beta_ \rightarrow ... \rightarrow \beta_{n-1} \rightarrow \beta_{n} = \omega$, the parser will first discover $\beta_{n - 1} \rightarrow \beta_{n}$, then $\beta_{n - 2} \rightarrow \beta_{n - 1}$, etc.

Parsing terminates when
1. $\beta_1$ reduced to $S$ (start symbol, success)
2. No match can be found (syntax error)

Key: given tokens we've already seen and the next input symbol (the lookahead), decide to:
1. Perform a reduction (and build parse tree)
	- Can reduce $A \rightarrow \beta$ if both these can hold:
		- $A \rightarrow \beta$ is a valid production, and
		- $A \rightarrow \beta$ is a step in this rightmost derivation
		- **shift-reduce** parser
2. Look ahead further

Bottom up parsers are typically known as **shift-reduce** parsers

Bottom up parsers are very efficient and typically used in most programming languages' compilers

**Sentential form**: If $S \rightarrow^* \alpha$, the string $\alpha$ is called a sentential form of the grammar

**Handle**: A production whose right hand-side matches a substring of the tree frontier that is part of the rightmost derivation of the current input string (i.e. the "correct" production)
- Formally: a handle of a right-sentential form $\gamma$ is a production $A ::= \beta$ and a position in $\gamma$ where $\beta$ may be replaced by $A$ to produce the previous right-sentential form in the rightmost derivation of $\gamma$

Key data structures:
- **Stack** holding the frontier of the tree
- **String** with the remaining input (tokens)
- A table that encodes a **push down automata**

Shift-reduce parser operations:
- **Reduce**: If the top of the stack is the right side of a handle $A ::= \beta$, pop the right side $\beta$ and push the left side $A$
- **Shift**: push the next input symbol onto the stack
- **Accept**: Announce success
- **Error**: Syntax error discovered

**Viable Prefix**: A prefix of a right-sentential form that can appear on the stack of the shift-reduce parser

**Avoiding DFA Rescanning**:
- **Observation 1**: No need to restart DFA after a shift. Stay in the same state and process next token (avoid backtracking)
- **Observation 2**: After a reduction, the contents of the stack are the same as before except for the new non-terminal on top (record state numbers on top of the stack)

Idea: Change the stack to contain pairs of states and symbols from the grammar

Shift-reduce parser's DFA can be encoded using 2 tables:
- **Action** table encodes what to do given the current state and the next input symbol
- **goto** table encodes the transitions to take after a reduction

**LR Parsing Algorithm**:
``` Pseudocode
tok = scanner.getToken();
while (true) {
	s = top of stack;
	if (action[s, tok] = si) {
		push tok;
		push i (state);
	} else if (action[s, tok] = rj) {
		pop 2 * length of right side of production j;
		uncovered_s = top of stack;
		push left side A of production j;
		push state goto[uncovered_s, A];
	} else if (action[s, tok] = accept) {
		return;
	} else {
		// no entry in action table
		report syntax error;
		halt or attempt recovery;
	}
}
```

**Items**: An **item** (**LR Item**) is a production with a dot in the right hand side
- e.g. Items for $A ::= X Y$
	- $A ::= . X Y$
	- $A ::= X . Y$
	- $A ::= X Y .$
- Dot represents a position in the production

Grammars can cause problems when constructing an LR parser
- **Shift-reduce conflicts**: can either shit or reduce (makes parser non-deterministic)
	- LR(1) cannot resolve this conflict
	- Needs to be resolved by fixing the grammar by preventing this problem from occurring in the first place
	- Use a parse tool with a "longest match" rule
		- Choose to shift instead of reduce
- **Reduce-reduce conflicts**: two different reductions are possible in a given state
	- Indicate a serious problem with the grammar
	- Need to fix the grammar

**Covering grammar**: a grammar that resolves the reduce-reduce conflict by merging the conflicting non-terminals into a single one
- Will generate some programs that are not generated by the original grammar
- Use the type checker or other static semantic analysis to weed out illegal programs later

**LR State Machine**:
- Idea: Build a DFA that recognizes handles

**Constructing**

**Closure Algorithm**:
```
Closure (S)
	repeat
		for any item [A ::= alpha . B beta] in S
			for all productions B ::= gamma
		add [B ::= . gamma] to S
	until S does not change
	return S	
```
- Example of a **fixed-point algorithm**
- Adds all items implied by items already in $S$

**Goto Algorithm**:
```
Goto (I, X)
	set new to the empty set
	for each item [A ::= alpha . X beta] in I
		add [A ::= alpha X . beta] to new
	return Closure(new)
```
- Where $I$ is a set of items and $X$ is a terminal
- $I$ is a set of items
- $X$ is a grammar symbol (terminal or non-terminal)
- $Goto$ moves the dot past the symbol $X$ in all appropriate items in set $I$

**LR(0) construction**:
1. Augment the grammar with an extra start production $S' ::= S \$$
2. Let $T$ be the set of states
3. Let $E$ be the set of edges
4. Initialize $T$ to Closure($[ S' ::= . S \$ ]$)
5. Initialize $E$ to empty
```
repeat
	for each state I in T
		for each item [A ::= alpha . X beta] in I
			Let new be Goto(I, X)
			Add new to T if not present
			Add edge I -> new to E if not present
until E and T do not change in this iteration
```

**Building the Parse Tables**
- For each edge $I \rightarrow_X J$
	- If $X$ is a terminal, put $sj$ in column $X$, row $I$ of the action table (shift to state $j$)
	- If $X$ is a non-terminal, put $gj$ in column $X$, row $I$ of the goto table (go to state $j$)
- For each state $I$ containing an item $[S' ::= S . \$ ]$, put accept in column $\$$ of row $I$
- For any state containing $[A ::= \gamma .]$ put action $rn$ (reduce) in every column of row $I$ in the table, where $n$ is the production number (not a state number)

A grammar is LR(0) if its LR(0) state machine has no shift-reduce or reduce-reduce conflicts

Because the grammar is CF, we cannot build a DFA for the entire grammar, rather we can build the DFA for the viable prefixes

A state is just a set of items
- **Start**: an initial set of items
- **Completion (or closure)**: additional productions whose left hand side appears to the right of the dot in some item already in the state

**SLR Parser**
- Use information about what can follow a non-terminal to decide if we should perform a reduction
- Don't reduce if the next input symbol can't follow the resulting non-terminal
- Must compute $\text{FOLLOW}(A)$ - the set of symbols that can follow $A$ in any possible derivation
	- Must first compute $\text{FIRST}(\gamma)$ for strings $\gamma$ that can follow $A$

All three are computed together
- **nullable(X)** is true if $X$ can derive the empty string
- Given a string $\gamma$ of terminals and non-terminals $\text{FIRST}(\gamma)$ is the set of terminals that can begin any strings derived from $\gamma$
- **FOLLOW(X)** is the set of terminals that can immediately follow $X$ in some derivation






