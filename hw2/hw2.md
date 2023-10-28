# Problem 1

**Grammar**:
`S ::= cSdS | dScS | ϵ`

1. Two leftmost derivations for the sentence `cdcd`

**First**: $cSdS \rightarrow cdScSdS \rightarrow cd \epsilon cSdS \rightarrow cdc \epsilon dS \rightarrow cdcd \epsilon \rightarrow cdcd$

**Second**: $cSdS \rightarrow c \epsilon dS \rightarrow cdcSdS \rightarrow cdc \epsilon dS \rightarrow cdcd \epsilon \rightarrow cdcd$

2. Two rightmost derivations for the sentence `cdcd`

**First**: $cSdS \rightarrow cSdcSdS \rightarrow c \epsilon dcSdS \rightarrow cdc \epsilon dS \rightarrow cdcd \epsilon \rightarrow cdcd$

**Second**: $cSdS \rightarrow cdScSdS \rightarrow cd \epsilon cSdS \rightarrow cdc \epsilon dS \rightarrow cdcd \epsilon \rightarrow cdcd$


# Problem 2

**Grammar**:
```
S ::= ( L ) | x
L ::= L , S | S
```

1. Left-most derivation of $(x, (x, x))$.

$S \rightarrow ( L ) \rightarrow ( L , S ) \rightarrow ( S , S ) \rightarrow ( x , S ) \rightarrow ( x , ( L ) ) \rightarrow ( x , ( L , S ) ) \rightarrow ( x , ( S , S ) ) \rightarrow ( x , ( x , S ) ) \rightarrow ( x , ( x , x ) )$

2. Right-most derivation of $(x, (x, x))$.

$S \rightarrow ( L ) \rightarrow ( L , S ) \rightarrow ( L , ( L ) ) \rightarrow ( L , ( L , S ) ) \rightarrow ( L , ( L , x ) ) \rightarrow ( L , ( S , x) ) \rightarrow ( L , ( x , x ) ) \rightarrow ( S , ( x , x ) ) \rightarrow ( x , ( x , x ) )$

3. Show the steps that a shift-reduce parser goes through when it parses $(x, x, x)$.

$$
\begin{array}{lrl}
\text{Stack} & \text{Input} & \text{Action} \\
\hline
$ & (x, x, x)$ & \text{shift} \\
$( & x, x, x)$ & \text{shift} \\
$(x & , x, x)$ & \text{reduce by } S \rightarrow x \\
$(S & , x, x)$ & \text{reduce by } L \rightarrow S \\
$(L & , x, x)$ & \text{shift} \\
$(L, & x, x)$ & \text{shift} \\
$(L, x & , x)$ & \text{reduce by } S \rightarrow x \\
$(L, S & , x)$ & \text{reduce by } L \rightarrow L , S \\
$(L & , x)$ & \text{shift} \\
$(L, & x)$ & \text{shift} \\
$(L, x & )$ & \text{reduce by } S \rightarrow x \\
$(L, S & )$ & \text{reduce by } L \rightarrow L , S \\
$(L & )$ & \text{shift} \\
$(L) & $ & \text{Reduce by } S \rightarrow (L) \\
$S & $ & \text{reduce by } L \rightarrow S \\
$L & $ & \text{accept}
\end{array}
$$

4. Swapping `L ::= L , S` production with `L ::= S , L` production.

$$
\begin{array}{lrl}
\text{Stack} & \text{Input} & \text{Action} \\
\hline
$ & (x, x, x)$ & \text{shift} \\
$( & x, x, x)$ & \text{shift} \\
$(x & , x, x)$ & \text{reduce by } S \rightarrow x \\
$(S & , x, x)$ & \text{shift} \\
$(S, & x, x)$ & \text{shift} \\
$(S, x & , x)$ & \text{reduce by } S \rightarrow x \\
$(S, S & , x)$ & \text{shift} \\
$(S, S, & x)$ & \text{shift} \\
$(S, S, x & )$ & \text{reduce by } S \rightarrow x \\
$(S, S, S & )$ & \text{reduce by } L \rightarrow S \\
$(S, S, L & )$ & \text{reduce by } L \rightarrow S , L \\
$(S, L & )$ & \text{reduce by } L \rightarrow S , L \\
$(L & )$ & \text{shift} \\
$(L) & $ & \text{reduce by } S \rightarrow (L) \\
$S & $ & \text{reduce by } L \rightarrow S \\
$L & $ & \text{accept}
\end{array}
$$

As we can see, the number of steps that the parser goes through remains the same. However, it will require more backtracking if an always reduce heuristic is used because making the reduction $L \rightarrow S$ on the first two $S$ will result in a non-acceptance state.


# Problem 3

**Grammar**:

```
Start ::= S
S ::= A a
A ::= B C | B C f
B ::= b
C ::= c
```

Parse table:

$$
\begin{array}{|c|c c c c c | c c c|}
\hline

\text{State} & a & b & c & f & $ & A & B & C & S \\

\hline

0 & & & & & \text{acc} \\ 
1 & & \text{s2} & \text{s3} & & & \text{g7} & \text{g4} \\ 
2 & \text{r4} & \text{r4} & \text{r4} & \text{r4} \\ 
3 & \text{r4} & \text{r4} & \text{r4} & \text{r4} \\ 
4 & & & \text{s5} & & & & & \text{g6} \\ 
5 & \text{r6} & \text{r6} & \text{r6} & \text{r6} \\
6 & \text{r1} & \text{r1} & \text{r1} & \text{s7}   \\ 
7 & \text{r8} & \text{r8} & \text{r8} & \text{r8} & \\ 
8 \\
9 & \text{r1} & \text{r1} & \text{r1} & \text{r1} \\

\hline
\end{array}
$$

From this parse table we see that the grammar is an LR(1) grammar because there are no shift reduce conflicts. The only potential question would be when the stack has the values `BC`, in which case the question of whether to shift or reduce arises. But since we have a lookahead value of 1 we can see if the next token is `f`, in which case we shift, or anything else, in which case we reduce.

# Problem 4

$\nabla \Delta$
Grammar:
$$
\begin{array}
 ::= \nabla
\end{array}
$$
```
S ::= ∇ S Δ | ϵ
```

Parse table:
$$
\begin{array}{|c| c c c | c |}
\hline

\text{State} & \nabla & \Delta & $ & S \\
\hline

0 &&& \text{acc} & \text{g1} \\
1 & \text{s2} &&&  \\ 
2 && \text{r0} && \text{g1} \\

\hline
\end{array}
$$

From this parse table we see that there are no shift-reduce conflicts meaning that the grammar is LR(1).