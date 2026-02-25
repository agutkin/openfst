# Encode/Decode

## Description

The `Encode` operation allows the representation of a weighted transducer as a
weighted automaton, an unweighted transducer or an unweighted automaton by
considering the pair (input label, output), the pair (input label, weight) or
the triple (input label, output label, weight) as a single label depending on
the value of the encode flags: `kEncodeLabels`, `kEncodeWeights` or
`kEncodeLabels|kEncodeWeights`. The encoding of each pair or triple of labels
and/or weights as a unique key is performed by an `EncodeMapper` object.

The `Decode` operation takes as input an encoded FST and the corresponding
`EncodeMapper` object and reverts the encoding.

## Usage

### EncodeMapper

```cpp
static const uint32 kEncodeLabels      = 0x0001;
static const uint32 kEncodeWeights     = 0x0002;
static const uint32 kEncodeFlags       = 0x0003;
```

```txt
enum EncodeType { ENCODE = 1, DECODE = 2 };
```

```cpp
template <class Arc> EncodeMapper<Arc>::
EncodeMapper(uint32 flags, EncodeType type);
```

[`EncodeMapper`](https://www.openfst.org/doxygen/fst/html/classfst_1_1EncodeMapper.html)

### Encode

```cpp
template <class Arc>
void Encode(MutableFst<Arc> *fst, EncodeMapper<Arc> *encoder);
```

```cpp
template <class Arc> EncodeFst<Arc>::
EncodeFst<Arc>(const Fst<Arc> &fst, EncodeMapper<Arc> *encoder);
```

[`EncodeFst`](https://www.openfst.org/doxygen/fst/html/classfst_1_1EncodeFst.html)

### Decode

```cpp
template <class Arc>
void Decode(MutableFst<Arc> *fst, const EncodeMapper<Arc> &encoder);
```

```cpp
template <class Arc> DecodeFst<Arc>::
DecodeFst<Arc>(const Fst<Arc> &fst, EncodeMapper<Arc> *encoder);
```

[`DecodeFst`](https://www.openfst.org/doxygen/fst/html/classfst_1_1DecodeFst.html)

### fstencode

```bash
fstencode [--encode_labels] [--encode_weights] in.fst encoder out.fst
```

```bash
fstencode --decode in.fst encoder out.fst
```

## Example

### A:

![Input FST for Encode/Decode.](images/encode1.png)

### Encode(&A, &encoder):

**`encode_flags`** | `kEncodeLabels`                                   | `kEncodeWeights`
------------------ | ------------------------------------------------- | ----------------
**`$flags`**       | `--encode_labels`                                 | `--encode_weights`
**`result`**       | ![Result of encoding labels.](images/encode2.png) | ![Result of encoding weights.](images/encode3.png)

**`encode_flags`** | `kEncodeLabels \| kEncodeWeights`
------------------ | ---------------------------------
**`$flags`**       | `--encode_labels --encode_weights`
**`result`**       | ![Result of encoding labels and weights.](images/encode4.png)

```bash
EncodeMapper<Arc> encoder(encode_flags, ENCODE);
Encode(&A, &encoder);
EncodeFst<Arc>(A, &encoder);

fstencode $flags a.fst encoder b.fst
```

### Decode(&A, encoder):

![Input FST for Encode/Decode.](images/encode1.png)

```bash
Decode(&A, encoder);
DecodeFst<Arc>(A, encoder);
fstencode --decode a.fst encoder b.fst
```

## Complexity

`Encode`, `Decode:`

*   Time: $O(V + E)$
*   Space: $O(1)$

where $V$ = # of states, and $E$ = # of transitions in the input FST.

`EncodeFst`, `DecodeFst:`

*   Time: $O(v + e)$
*   Space: $O(1)$

where $v$ = # of states visited, $e$ = # of arcs visited Constant time and
to visit an input state or arc is assumed and exclusive of
[caching](advanced_usage.md#caching).
