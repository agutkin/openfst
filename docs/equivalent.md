# Equivalent

## Description

This operation determines if two epsilon-free deterministic weighted acceptors
are [equivalent](glossary.md#equivalent), that is if they accept the same
strings with the same weights.

## Usage

```cpp
template <class Arc>
bool Equivalent(const Fst<Arc> &fst1,
                const Fst<Arc> &fst2,
                double delta = kDelta);
```

```bash
fstequivalent a.fst b.fst
```

## Examples

**`A`**                                                      | **`B`**                                                      | **`C`**
------------------------------------------------------------ | ------------------------------------------------------------ | -------
![Input FST A for Equivalence operation.](images/equiv1.png) | ![Input FST B for Equivalence operation.](images/equiv2.png) | ![Input FST C for Equivalence operation.](images/equiv3.png)

```bash
Equivalent(A, B);  // returns true
Equivalent(A, C);  // returns false

$ if fstequivalent a.fst b.fst; then echo true; else echo false; fi
true
$ if fstequivalent a.fst c.fst; then echo true; else echo false; fi
false
```

## Complexity

`Equivalent`

*   Time:

*   *Unweighted*: quasi-linear, i.e. $O(d n G(n))$

*   *Weighted*: complexity of unweighted + complexity of
    [weight-pushing](push.md#complexity)

*   Space: linear, i.e. $O(n + d)$

where $n = V_1 + V_2$ with $V_i$ = # of states, $d$ is the maximal
out-degree and $G(n)$ is a very slowly growing function that can be
approximated by 4 by all practical purposes.

## Caveats

Weighted equivalence is sensitive to machine precision when using
floating-point-based weights especially with non-integral values. Consider using
[RandEquivalent](rand_equivalent.md) instead.

## See Also

[Equal](equal.md), [Isomorphic](isomorphic.md),
[RandEquivalent](rand_equivalent.md)

## References

*   Alfred V. Aho, John E. Hopcroft, and Jeffrey D. Ullman. "The Design and
    Analysis of Computer Programs". Addison-Wesley, 1974.
