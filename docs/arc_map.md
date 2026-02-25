# ArcMap

## Description

This operation transforms each arc and final state in the input FST. The
transformation is specified by a function object called an *arc mapper*.

For instance, `RmWeightMapper`
[`rm_weight_mapper`](https://www.openfst.org/doxygen/fst/html/structfst_1_1_rm_weight_mapper.html)
replaces the weight of every arc and final state by 1.

A list of available arc mappers and instructions on how to create them are given
[here](advanced_usage.md#arc-mappers).

## Usage

```cpp
template <class Arc, class ArcMapper>
ArcMap(MutableFst<Arc> *fst, ArcMapper *mapper);
```

```cpp
template <class Arc, class ArcMapper>
ArcMap(MutableFst<Arc> *fst, ArcMapper mapper);
```

```cpp
template <class Arc, class ArcMapper>
ArcMap(const Fst<Arc> &ifst, MutableFst<Arc> *ofst, ArcMapper *mapper);
```

```cpp
template <class Arc, class ArcMapper>
ArcMap(const Fst<Arc> &ifst, MutableFst<Arc> *ofst, ArcMapper mapper);
```

```cpp
template <class Arc, class ArcMapper> ArcMapFst<Arc>::
ArcMapFst(const Fst<A> &fst, ArcMapper *mapper);
```

[`ArcMapFst`](https://www.openfst.org/doxygen/fst/html/classfst_1_1ArcMapFst.html)

```cpp
template <class Arc, class ArcMapper> ArcMapFst<Arc>::
ArcMapFst(const Fst<A> &fst, const ArcMapper &mapper);
```

```bash
fstmap [--opts] in.fst out.fst
    -delta (Comparison/quantization delta) type: double default: 0.0009765625
    -map_type (Map operation, one of: "identity", "invert", "plus (--weight)",
      "quantize (--delta)", "rmweight", "superfinal", "times (--weight)",
      "to_log", "to_log64", "to_standard"
      ) type: string default: "identity"
    -weight (Weight parameter) type: string default: ""
```

Note `fstmap` also includes [state mappers](state_map.md).

## Example

### A:

![Input FST for ArcMap/StateMap.](images/map1.png)

### ArcMap(&A, RmWeightMapper()):

![Result of the mapping operation.](images/map2.png)

```bash
ArcMap(&A, RmWeightMapper<StdArc>());
ArcMap(A, &B, RmWeightMapper<StdArc>());
ArcMapFst B(A, RmWeightMapper<StdArc>());

fstmap --map_type=rmweight a.fst b.fst
```

## Complexity

`ArcMap:`

*   Time: $O(c(V + E))$
*   Space: $O(m)$

where $V$ = # of states, $E$ = # of arcs in input FST, $c$ = cost of
processing one arc by the mapper and $m$ = total memory usage for the mapper.

`ArcMapFst:`

*   Time: $O(c(v + e))$
*   Space: $O(m)$

where $v$ = # of visited states, $e$ = # of visited arcs in input FST,
$c$ = cost of processing one arc by the mapper and $m$ = total memory usage
for the mapper. Constant time and space to visit an input state or arc is
assumed and exclusive of [caching](advanced_usage.md#caching).

For instance in the case of `RmWeightMapper`, we have $c = O(1)$ and $m =
O(1)$.

## See Also

[Arc Mappers](advanced_usage.md#arc-mappers), [StateMap](state_map.md)
