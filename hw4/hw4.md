# Problem 1

(a) Grammar:

0. `exp' ::= exp $`
1. `exp ::= id`
2. `exp ::= ( exp )`
3. `exp ::= ( type ) exp`
4. `type ::= id`


$FIRST(exp) = \lbrace id, ( \rbrace$

$FIRST(exp') = FIRST(exp) = \lbrace id, ( \rbrace$

$FIRST(type) = \lbrace id \rbrace$

$FOLLOW(exp) = \lbrace \$ \rbrace$

$Nullable(exp) = false$


(b) Grammar:

0. `eqns' ::= eqns $ ($ is the end-of-file marker)`
1. `eqns ::= eq sup eqns`
2. `eqns ::= eq`
3. `eq ::= x`


# Problem 2

(a) Symbol table:


(b) Actions required for symbol table management:


# Problem 3



# Problem 4


# Problem 5

