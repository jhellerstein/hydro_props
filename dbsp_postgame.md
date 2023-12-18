## High-level comparisons
  - In terms of semantics, DBSP already fits OK into Hydroflow:
    - idempotence can be mechanically enforced on Abelian Groups over the network ("upgrade")  
    - we *can* support lattices that happen to have inverses/difference and an identity
  - In terms of performance, DBSP is all about incremental state maintenance
    - We don't have easy ways to handle deltas incrementally, particularly for non-monotone subflows.
    - We don't have any way to do semi-naive evaluation of recursive queries due to the absence of iteration-based difference
  - DBSP's persistence and time semantics appear to be well-defined and consistent
      - We curently have confusion around state persistence semantics in our ops
      - We haven't done our rewrite rules to "push around" persistence in a graph

    
## Adopting DBSP ideas in Hydroflow?
  - It appears we can "emulate" DBSP in current Hydroflow (modulo per-iteration tricks)
  - If we want to integrate lattices and Abelian groups, we need to think about 
      - how do we expose this in the type/property system,
      - what rewrites/analyses are allowed/disallowed
  - Alternatively, it seems like we could achieve Dedalus compatibility with DBSP semantics
      - Do we need idempotence? Non-invertible lattices?
        - **I think so**. Seems like Vector Clock is an example of a non-invertible lattice that we want.

## Next steps
  1. To get our hands dirty, we could write Anna with causal consistency using a mix of VCs and Z-sets
  2. Separately, decide where state is maintained:
      - Inside every stateful op, like join and fold?
      - Require stateful ops to have some persist op as the previous op? Akin to DBSP I operator.
          - Shared state, or a cumulative flow passed by ref? Whats the diff really?
  4. Don't get terribly distracted by incremental maintenance.
      - Revisit our "properties" doc to cover cases where the DBSP Proposition 3.2 can be applied
      - Ensure that we can't accidentally mis-apply DBSP rewrites to non-(Abelian Group) data
  5. Stretch goal: `Persistence::Iteration` lifetime fo semi-naive
      
