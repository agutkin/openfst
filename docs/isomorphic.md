# Isomorphic

## Description

This operation determines if two transducers with a
[certain required determinism](isomorphic.md#caveats) have the same states,
irrespective of numbering, and the same transitions with the same labels and
weights, irrespective of ordering. In other words, `Isomorphic(A, B)` is true if
and only if the states of A can be renumbered and the transitions leaving each
state reordered so that [Equal(A, B)](equal.md) is true.

## Usage

```cpp
template <class Arc>
bool Isomorphic(const Fst<Arc> &fst1,
                const Fst<Arc> &fst2,
                double delta = kDelta);
```

fstisomorphic a.fst b.fst |

## Examples

**`A`**                    | **`B`**                                      | **`C`**
-------------------------- | -------------------------------------------- | -------
![FST A.](images/iso1.png) | ![FST B (isomorphic to A).](images/iso2.png) | ![FST C (not isomorphic to A).](images/iso3.png)

```bash
Isomorphic(A, B);  // returns true
Isomorphic(A, C);  // returns false

$ if fstisomorphic a.fst b.fst; then echo true; else echo false; fi
true
$ if fstisomorphic a.fst c.fst; then echo true; else echo false; fi
false
```

## Complexity

`Isomorphic`

*   Time: $O(V_1 + V_2 + E_1 \log D_1 + E_2 \log D_2)$
*   Space: $O(V_1 + V_2 + D_1 + D_2)$

where $V_i$ = # of states, $E_i$ = # of transitions, $D_i$ = maximum
[out-degree](glossary.md#out-degree)

## Caveats

The inputs should be deterministic in the sense that no state has two
transitions with the same input label, output label and weight (e.g.,
deterministic after [Encode](encode_decode.md) is performed on the labels and
weights). If non-determinism in this sense is encountered, an error is raised.
This requirement is imposed since the general solution is *graph isomorphism
complete*, for which no known polynomial-time algorithm exists.

## See Also

[Equal](equal.md), [Equivalent](equivalent.md),
[RandEquivalent](rand_equivalent.md)

## References

Zemlyachenko, Viktor N., Nickolay M. Korneenko, and Regina I. Tyshkevich. "Graph
isomorphism problem." Journal of Soviet Mathematics 29.4 (1985): 1426-1481.
