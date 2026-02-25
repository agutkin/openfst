# Verify

## Description

This operation checks the sanity of a FST's contents. It returns false if the
transducer is incomplete or ill-formed (e.g., a non-trivial FST that has no
initial state or transitions to non-existent destination states).

## Usage

```cpp
template<class Arc>
bool Verify(const Fst<Arc> &fst);
```

## Complexity

`Verify`:

*   Time: $O(V + E)$
*   Space: $O(1)$

where $V$ = # of states and $E$ = # of arcs.
