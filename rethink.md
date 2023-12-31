# Stream and Operator Properties
## Goals
- *Free replication/decoupling*: component is ACI wrt input domain
- *Free partitioning*: component is key-wise computable
- *Incremental evaluation (streaming)*: difference/sum operators
- *Free termination*: $\top$ or ??
- *Cycles with Fixpoints*: POPS
    - with Incremental evaluation: Complete, Distributive Dioid
- *Operator reordering*: commutativity/distributivity of ops

## Properties and axioms
### Stream properties
- type (incl domain constraints like $\top$, FDs)
- ordering identifier (for *non-commutative*)
- batching identifier (for *non-associative*)
- partition identifier
- differential?
    - Each item in stream carries new information wrt order of $+$ ($\forall i, j: s_i \sqsubset (s_i + s_j)$ ) (for incremental eval)
    - Or simply an *I* vs *D* annotation that upstream has/hasn't been integrated?
      - Side question: why doesn't DBSP model differentiated data as a nested stream?
- monotonicity (order by) wrt some partial order
- length bound
### Single-stream operator properties
Assume binary operator $+$ applied pairwise on the stream elements. Compare with DBSP Proposition 3.2. 
- Commutativity: $a + b = b + a$
- Associativity: $a + (b + c) = (a + b) + c$
- Idempotence: $a + a = a$
- Monotonicity: $a + b \ge a$ (corollary of A+C+I)
- Differentiability/Inverse: is the datalog $^o$ definition sufficiently general for us?
    - $b - a \equiv \bigwedge \\{ c | a + c \sqsupseteq b \\}$
- Statelessness (invariance to differentiation)
- Key-wise computability
    - requires a corresponding $\bigoplus$ operator to reassemble
- Enforcement or Preservation of stream properties
### N-ary stream operator properties
Generalize above to handle product and individual properties: $xy + x + y$

### Compositions
#### Type composition
What do we want to say about these in general? Need composition of ops as well. See FAQ paper for tricky cases.
- product: free and lexical
- map
- set
- multiset
#### Pairwise operator composition
See DBSP Proposition 3.2
- Commutativity of two unary ops: $f(g(x)) = g(f(x))$
- Distributivity of an op wrt a binary op:
    - "linear" unary morphism: $f(a+b) = f(a) + f(b)$
    - "bilinear" distributivity:
        - $f((a+b), c) = f(a,c) + f(b,c)$
        - $f(a,(c+d)) = f(a,d) + f(c,d)$
- Alignment of property enforcement/preservation
  
# Background
## Properties we can use
- Lattices: ACI $\sqcup$ enables **free replication** and **free asynchronous decoupling** (CALM).
    - Morphisms: Distributivity of a monotone lattice function through merge enables **incremental evaluation**
        - Including semi-naive evaluation (Bloom $^L$)
    - Existence of $\top$ enables **free termination**, particularly useful when we can map to small domains.
- Abelian Groups: AC+Inverse enables differentiation and hence **incremental evaluation** (DBSP)
- (Pre-)Semirings: Distributivity of $\times$ over $+$ enables **query optimizations**
    - $+$ is a commutative monoid (AC+identity)
    - $\times$ is a monoid (A+identity)
    - In a pre-semiring, $\times$ need not be absorptive (i.e. $0 \times x \ne 0$)
    - POPS: a pre-semiring with a partially ordered domain, where $+$ is a commutative monoid, $\times$ is a monoid. (datalog $^o$)
        - for certain classes of monotone functions, **fixpoint is well-defined**
    - Complete, Distributive Dioid: enables **semi-naive evaluation**
        - Dioid: A semiring for which $+$ is idempotent (Khamis et al. definition from datalog $^o$)
        - $(S, +)$ forms a complete, distributive lattice: every set $A \subseteq S$ has GLB $\bigwedge A$, and $x \vee \bigwedge A = \bigwedge \\{ x \vee a | a \in A \\}$.
        - Difference defined by $b - a \equiv \bigwedge \\{ c | a + c \sqsupseteq b \\}$. As a result, if $a \sqsubseteq b$:
            1. $a + (b - a) =  b$
            2. $(a + b) - (a + c) = b - (a + c)$
- Distributivity through partition allows **free partitioning**
    - given semiring $(S, \oplus, \otimes)$, partition function $P:S \rightarrow \mathbb{N}$:
    - $\bigotimes(\bigoplus(R \subseteq S)) = \bigoplus_{i \in n}(\bigotimes(\\{s \in R | P(s) = i\\})$

## Algebraic Upgrades
- Idempotence (Commutative Monoid -> Lattice): via nonces/sequences. OnceTree to get linear space.
- datalog $^o$ paper (Sec 2.5.1) shows upgrades for some pre-semiring to POPS
- FAQ paper (appendix B) shows upgrades for some non-semirings to semirings

## Hybrid Algebras
- FAQ allows a "vector of semirings" for sum-product evaluation
