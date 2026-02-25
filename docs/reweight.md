# Reweight

## Description

This operation reweights an FST according to the potentials and in the direction
specified by the user. An arc of weight $w$, with an origin state of potential
$p$ and destination state of potential $q$, is reweighted by $p^{-1}
\otimes (w \otimes q)$ when reweighting towards the initial state and by $(p
\otimes w) \otimes q^{-1}$ when reweighting towards the final states.

The weights need to be left distributive when reweighting towards the initial
state and right distributive when reweighting towards the final states (valid
for `TropicalWeight` and `LogWeight`).

## Usage

```cpp
enum ReweightType { REWEIGHT_TO_INITIAL, REWEIGHT_TO_FINAL };
template <class Arc>
void Reweight(MutableFst<Arc> *fst, vector<typename Arc::Weight> potential, ReweightType type)
```

```bash
fstreweight [--opts] a.fst potentials.txt out.fst
    --to_final: type = bool, default = false
      Push/reweight to final (vs. to initial) states
```

## Complexity

`Reweight`:

*   Time: $O(V + E)$
*   Space: $O(V + E)$

where $V$ = # of states and $E$ = # of arcs.
