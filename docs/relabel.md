# Relabel

## Description

This operation relabels the input and/or output labels of an FST. The input
and/or output relabeling are specified by providing the corresponding sets of
pairs `(old_label, new_label)` of label IDs .

When the FST has input and/or output symbol tables, the relabeling can be
specified by giving new input and/or output symbol tables. The operation then
modifies the label IDs of each arc in such a way that the symbols are preserved.

## Usage

```cpp
template <class Arc>
void Relabel(
    MutableFst<Arc> *fst,
    const vector<pair<typename Arc::Label, typename A::Label> >& ipairs,
    const vector<pair<typename Arc::Label, typename A::Label> >& opairs);
```

```cpp
template<class Arc>
void Relabel(
     MutableFst<Arc> *fst,
     const SymbolTable* new_isymbols,
     const SymbolTable* new_osymbols);
```

```cpp
template <class Arc> RelabelFst<Arc>::
RelabelFst(const Fst<A>& fst,
             const vector<pair<Label, Label> >& ipairs,
             const vector<pair<Label, Label> >& opairs);
```

[`RelabelFst`](https://www.openfst.org/doxygen/fst/html/classfst_1_1RelabelFst.html)

```cpp
template <class Arc> RelabelFst<Arc>::
RelabelFst(const Fst<A>& fst,
             const SymbolTable* new_isymbols,
             const SymbolTable* new_osymbols)
```

```bash
fstrelabel [--relabel_ipairs=$ipairs] [--relabel_opairs=$opairs] in.fst out.fst
```

```bash
fstrelabel [--relabel_isymbols=$isyms] [--relabel_osymbols=$osyms] in.fst out.fst
```

## Examples

### A:

**`S1`** |
-------- |
`eps`    |
`a`      |
`b`      |
`c`      |
`d`      |
`f`      |
`g`      |

**`A`**                                                     |
----------------------------------------------------------- |
![Example of an FST before relabeling.](images/relabel.png) |

### Relabel(&A):

**`S2`** |
-------- |
`eps`    |
`a`      |
`b`      |
`c`      |
`d`      |
`f`      |
`g`      |

**`B`**                                                     |
----------------------------------------------------------- |
![Example of an FST before relabeling.](images/relabel.png) |

```bash
Relabel(&A, S2, S2);
RelabelFst<Arc> B(A, S2, S2);

fstrelabel  --relabel_isymbols=s2 --relabel_osymbols=s2 a.fst b.fst
```

**`P`** |
------- |
`0`     |
`1`     |
`2`     |
`3`     |
`4`     |
`5`     |
`6`     |

```bash
Relabel(&A, P, P);
RelabelFst<Arc> B(A, P, P);

fstrelabel --relabel_ipairs=p --relabel_opairs=p a.fst b.fst
```

## Complexity

`Relabel`

*   Time: $O(V + E + S_i + S_o)$
*   Space: $O(S_i + S_o)$

where $V$ = # of states, $E$ = # of arcs of the input FST, $S_i$ = # of
input symbols and $S_o$ = # of output symbols.

`RelabelFst`

*   Time: $O(v + e + S_i + S_o)$
*   Space: $O(S_i + S_o)$

where $v$ = # of states visited, $e$ = # of arcs visited of the input FST,
$S_i$ = # of input symbols and $S_o$ = # of output symbols. Constant time
and space to visit an input state or arc is assumed and exclusive of
[caching](advanced_usage.md#caching).
