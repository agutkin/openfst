# Equal

## Description

This operation determines if two transducers have the same states with the same
numbering and the same transitions with the same labels and weights in the same
order.

## Usage

```cpp
template <class Arc>
bool Equal(const Fst<Arc> &fst1,
           const Fst<Arc> &fst2,
           double delta = kDelta);
```

```bash
fstequal a.fst b.fst
```

## Examples

**`A`**                                                                                             | **`B`**                                                                                             | **`C`**
--------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | -------
![An FST with states 0, 1, 2, and 3, used to demonstrate equality and isomorphism.](images/eq1.png) | ![An FST with states 0, 1, 2, and 3, used to demonstrate equality and isomorphism.](images/eq1.png) | ![The same FST as eq1.png but with states 1 and 2 renumbered.](images/eq2.png)

```bash
Equal(A, B);  // returns true
Equal(A, C);  // returns false

$ if fstequal a.fst b.fst; then echo true; else echo false; fi
true
$ if fstequal a.fst c.fst; then echo true; else echo false; fi
false
```

## Complexity

`Equal`

*   Time: linear i.e. $O(V_1 + V_2 + E_1 + E_2)$
*   Space: $O(1)$

where $V_i$ = # of states and $E_i$ = # of transitions

## See Also

[Equivalent](equivalent.md), [Isomorphic](isomorphic.md),
[RandEquivalent](rand_equivalent.md)
