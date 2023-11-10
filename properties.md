## Stream Properties
Stream $S$ characterized by properties: $(V, O, P, T, M, @, X)$
- $V$: a multiset of values
- $O$: a total *order* of arrival
- $P$: a *parenthesization* (batching).
- $T$: a *type*
- $M$: if $T$ has a corresponding partial order $\le_T$, the *monotonicity* relationship between $\le_T$ and $O$ (monotonic, anti-monotonic, non-monotonic)
- $@$: if each item $x$ in the stream is an *atom* of $T$, i.e. $\forall x \in S y \in T, \; \; y \le_T x \implies y = \bot$. If true, we say the stream is *atomistic*.
- $X$: if all pairs of items in the stream are *exclusive* in $T$: $\forall x, y \in S z \in T, \;\; (z \le_T x \wedge z \le_T y) \implies z = \bot$. If true, we say the stream is *exclusive*.

> **Observation**: For atomistic streams, exclusivity is equivalent to $V$ being duplicate-free. 

> **Observation**: Monotonic $\implies$ Non-Atomistic (and Atomistic $\implies$ Non-Monotonic)

> **Observation**: Monotonic $\implies$ Non-Exclusive (and Exclusive $\implies$ Non-Monotonic)

## Operator Properties
Operators have input and output streams.

### Unary Operators and Property Transitions
For unary stream operator $\Omega: S_{in} \rightarrow S_{out}$, we 
characterize the *Property Transitions* between the input and output streams:
- *Invariant*: the property of the output is independent from that of the input
- *Preserved*: the property of the output is identical to that of the input
- *Deterministic*: the property of the output is some (non-identity) deterministic function of the input, or 
- *Non-deterministic*: the property of the output is non-deterministic.

Note that property transitions may be defined across different properties: e.g., the order of the input might affect the parenthesization of the output. **This is unpleasant: in principle have to characterize the cross-product of properties!**

Property transitions capture traditional algebraic properties:
- A unary operator is *Associative* if *all* properties of $S_{out}$ are invariant to the parenthesization of the input, $P_{S_{in}}$.
- A unary operator is *Commutative* if *all* properties of $S_{out}$ are invariant to the order of the input, $O_{S_{in}}$.
- A unary operator is *Idempotent*, if *all* properties of $S_{out}$ are invariant to duplication of elements in the input $V_{in}$. **XXX align with exclusivity rather than duplication?**



### Operators of Arbitrary Arity
More generally, for an operator $\Omega$ with many inputs and/or outputs, we can consider each pair of input/output streams $S_i, S_o$ and define operator properties for $S_i$ w.r.t. $S_o$ independently, making no assumptions about the properties of the other input streams.

We say that an operator is *fully $X$* if all its property transitions are $X$. For example, an operator can be *fully deterministic*.

## Replication Checks
We say that a dataflow graph composed of streams and operators can be *freely replicated* if for all input streams, it is guaranteed to produce the same output streams each time it runs.

> **Observation**: If all the operators in a dataflow graph are fully deterministic, the graph is freely replicable.

> **Lemma: Semi-Lattice operators are monotonic**: If an output stream $S_o$ of an operator $\Omega$ is associative, commutative and idempotent w.r.t. all inputs, then the type $T_o$ is constrained to be isomorphic to the join semi-lattice $L_{(V_o, \Omega)}$, and hence is monotonic. *(Standard lattice theory.)*

> **Theorem (unidirectional CALM): Monotonic operators are freely replicable**: If the inputs of a dataflow graph have deterministic values $V$, and each output stream $S_j$ of the graph is monotonic with respect to some fixed partial order $O_j, the graph is freely replicable. *(Follows from CALM Theorem, but would be nice to show inductively via local properties on operators/edges in the graph, perhaps induction in expression/path length starting with a single "source" op. Alternatively we could just argue that Semi-Lattice operators are freely replicable and leave it at that, but lines up less well with bidirectionality of CALM).*

## Example Operators of Interest
### source_iter
- Deterministic in all properties.

### network
- Non-deterministic in $V, O, P, M, X$. Preserves $T, @$.

### lattice_merge ('static)
- Deterministic in all properties.
- Preserves $P$ **More like a morphism of P???**
- Output is monotonic wrt the lattice type (hence not $@$ or $X$)

### delta_to_atoms ('static)
- Deterministic in all properties
- Preserves $P$ **More like a morphism?**
- Output is $@,X$ (hence non-monotone)

### delta ('static)
- Deterministic in all properties
- Preserves $P$ **More like a morphism**
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
