## Stream Properties
Stream $S$ characterized by properties: $(V, O, P, T, M, @, X)$
- $V$: a set of values
- $O$: a total *order* of arrival
- $P$: a *parenthesization* (batching).
- $T$: a *type*
- $M$: if $T$ has a corresponding partial order $\le_T$, the *monotonicity* relationship between $\le_T$ and $O$ (monotonic, anti-monotonic, non-monotonic)
- $@$: if each item $x$ in the stream is an *atom* of $T$, i.e. $\forall x \in S y \in T, \; \; y \le_T x \implies y = \bot$. If true, we say the stream is *atomistic*.
- $X$: if all pairs of items in the stream are *exclusive* in $T$: $\forall x, y \in S z \in T, \;\; (z \le_T x \wedge z \le_T y) \implies z = \bot$. If true, we say the stream is *exclusive*.

> **Observation**: In a stream that is both atomistic and exclusive, each item is a distinct value from $T$.

> **Observation**: Monotonic $\implies \neg$ Atomistic (and Atomistic $\implies$ Non-Monotonic)

> **Observation**: Monotonic $\implies \neg$ Exclusive (and Exclusive $\implies$ Non-Monotonic)

## Operator Properties
Operators have input and output streams.

### Unary Operators
For unary stream operator $\Omega: S_{in} \rightarrow S_{out}$, we say it is:
- $A$ (*Associative*), if $S_{out}$ is invariant to changes in parenthesization $P_{S_{in}}$.
- $C$ (*Commutative*), if $S_{out}$ is invariant to changes in order $O_{S_{in}}$.
- $I$ (*Idempotent*), if $S_{out}$ is invariant to arbitrary duplication of elements in $S_{in}$

We also characterize $\Omega$ in terms of the relationship between stream properties of its output with respect to its input. For each property, the operator's behavior can be marked:
- *Preserve*: the property of the output will be that of the input
- *Deterministic*: the property of the output is a deterministic function of the input, or 
- *Non-deterministic*: the property of the output is non-deterministic.

### Operators of Arbitrary Arity
More generally, for an operator $\Omega$ with many inputs and/or outputs, we can consider each pair of input/output streams $S_i, S_o$ and define operator properties for $S_i$ w.r.t. $S_o$ independently, making no assumptions about the properties of the other input streams. We use the notation $A_{io}, C_{io}, I_{io}$, etc.

We say that an operator is *fully deterministic* if each output stream's properties are deterministic with respect to all its input streams.

## Replication Checks
We say that a dataflow graph composed of streams and operators can be *freely replicated* if for all input streams, it is guaranteed to produce the same output streams each time it runs.

> **Observation**: If all the operators in a dataflow graph are fully deterministic, the graph is freely replicable.

> **Observation**: If an output stream $S_o$ of an operator $\Omega$ has all the properties ACI w.r.t. all inputs, then $S_o$ is monotonic wrt the join semi-lattice $L_{(T_o,\Omega)}$. (Standard properties of lattice theory).

> **Lemma**: If the inputs of a dataflow graph are deterministic in their Values, and each output edge of the graph is monotonic with respect to some fixed partial order, the graph is freely replicable. (Follows from CALM Theorem, but would be nice to show inductively via  local properties on operators/edges in the graph, perhaps induction in expression/path length starting with a single "source" op).

## Example Operators of Interest
### source_iter
- Deterministic in all properties.

### network
- Non-deterministic in $V, O, P, M$. Preserves $T, @, X$.

### lattice_merge ('static)
- Deterministic in all properties.
- Preserves $P$ **???**
- Output is monotonic wrt the lattice type (hence not $@$ or $X$)

### atom_delta ('static)
- Deterministic in all properties
- Preserves $P$ **???**
- Output is $@,X$ (hence non-monotone)

### delta ('static)
- Deterministic in all properties
- Preserves $P$ **???**
- Output is none of $M,@,X$
- 
## Algebraic Upgrades
- Looking at this lattice data model and the three ACI properties, associativity commutativity, and idempotence, a natural question is "what classes of programs can fit into this model?"
- This looks like a question of computability - we can achieve coordination-free execution if and only if we are ACI, and therefore only functions that are ACI can be computed coordination-free - but this is wrong!
- Some of these properties can be enforced programmatically, such as idempotence and commutativity, so the presence of those properties is irrelevant to whether a function can be computed coordination-free.
- Instead, the question becomes "how much metadata do we need to enforce the properties we need in the distributed setting?".
- We have now converted what looks like a computability question into a question of space complexity - do we need constant metadata? metadata linear in the number of replicas? metadata linear in the number of messages?
- The study of algebraic properties that allow for certain transformations and correctness guarantees has been popular outside of distributed systems recently as well e.g. semi-ring provenance, incremental view maintenance, datalog^o, dbsp, different kinds of monoids in automata theory.
- open questions:
- catalogue the useful algebraic properties in data systems
- what is the minimal amount of metadata needed to enforce these properties?
- what properties are impossible to enforce via metadata (lower bounds)
- We call this process of tagging on metadata needed to enforce any missing properties "algebraic upgrades".
- We are interested in building an optimizer for arbitrary programs that can tag on the minimal metadata necessary to guarantee invariants such as consistency in different settings. 
