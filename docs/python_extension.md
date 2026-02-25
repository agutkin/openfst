# OpenFst Python extension

This extension exposes the OpenFst [scripting API](advanced_usage.md#fstscript)
to [Python](https://www.python.org/) (3.6 or better). Like the scripting API, it
supports [arbitrary arcs and weights](advanced_usage.md#arcs). The extension
allows for rapid prototyping and interactive construction of FSTs using the
Python REPL.

Note that this extension is unrelated to, and incompatible with, any other
third-party Python extensions for OpenFst (e.g.,
[pyfst](http://pyfst.github.io/)).

To install this package, either:

*   issue `--enable-python` during configuration of OpenFst
*   Or, install OpenFst with FAR support (`--enable-far`) then install the
    [PyPi](https://pypi.python.org/pypi/openfst) package `openfst` using
    [Pip](https://pip.pypa.io/en/stable/): `pip install openfst`

NB: `>>>` indicates the Python interactive prompt; all other typewriter-text
lines are print to stdout or stderr.

## Module import

The Python module itself is called `pywrapfst` but in this tutorial, we will
alias it to `fst`.

```txt
>>> import pywrapfst as fst
```

## FST construction

FSTs can be compiled from [arc-lists](quick_tour.md#fstcompile) in the same
format used by the `fstcompile` binary.

```txt
>>> compiler = fst.Compiler()
>>> print >> compiler, "0 1 97 120 .5"
>>> print >> compiler, "0 1 98 121 1.5"
>>> print >> compiler, "1 2 99 123 2.5"
>>> print >> compiler, "2 3.5"
>>> f = compiler.compile()   # Creates the FST and flushes the compiler buffer.
>>> f.num_states
3
>>> f.final(2)
<tropical Weight 3.5 at 0x1215ed0>
```

FSTs can be read in from disk using `Fst.read`, which takes a string argument
specifying the input file's location.

```txt
>>> v = fst.Fst.read("vector.fst")
```

This class method takes an optional second argument, a string indicating the
desired [FST type](advanced_usage.md#fst-types). The FST is converted to this
type if it the on-disk FST is not already of the desired type.

```txt
>>> c = fst.Fst.read("const.fst")
>>> c.fst_type
'const'
>>> v = fst.Fst.read("const.fst", fst_type="vector")
>>> v.fst_type
'vector'
```

This conversion can also be accomplished after instantiation using the `convert`
function.

```txt
>>> v = fst.convert(c, fst_type="vector")
>>> v.fst_type
'vector'
```

Note that this conversion to the `vector` FST type is mandatory if one wishes to
perform [mutation operations](python_extension.md#fst-mutation) on an `const`
FST.

FSTs can be read in from [FST Archives (FARs)](extensions.md#fst-archives-fars)
using the `FarReader` object.

```txt
>>> reader = fst.FarReader.open("lattice.far")
```

Each FST stored within a FAR has a unique string ID which can be used to extract
it from the reader object.

```txt
>>> f = reader["1best"]
```

Or, all FSTs stored within a FAR may be accessed via iteration over the reader
object.

```txt
>>> for (name, f) in reader:
...     print name, f.num_states
('1best', 23)
('2best', 27)
('3best', 27)
...
```

Finally, an empty mutable vector FST can be created using `VectorFst`.

```txt
>>> f = fst.VectorFst()
```

By default, the resulting FST uses `standard` (tropical-weight) arcs, but users
can specify other arc types (e.g., log) via an optional argument.

```txt
>>> f.arc_type
'standard'
>>> g = fst.VectorFst("log")
>>> g.arc_type
'log'
```

## FST object attributes and properties

All FSTs have the following read-only attributes ("properties" in Python
jargon):

`arc_type`       | A string indicating the arc type
---------------- | -------------------------------------------------------------
`input_symbols`  | The input `SymbolTable`, or `None` if no input table is set
`fst_type`       | A string indicating the FST (container) type
`output_symbols` | The output `SymbolTable`, or `None` if no output table is set
`start`          | The state ID for the start state
`weight_type`    | A string indicating the weight type

Mutable FSTs also provide the `num_states` attribute, which indicates the number
of states in the FST.

To access FST properties (i.e., cyclicity, weightedness), use the `properties`
method.

```txt
>>> print "Is f cyclic?", f.properties(fst.CYCLIC, True) == fst.CYCLIC
Is f cyclic? True
```

## FST access and iteration

FST arcs and states can be accessed via the `StateIterator`, `ArcIterator`, and
`MutableArcIterator` objects. These are most naturally constructed using the
`states` and `arcs` methods, as follows.

```txt
>>> for state in f.states():
...     for arc in f.arcs(state):
...         print state, arc.ilabel, arc.olabel, arc.weight, arc.nextstate
0 97 120 1.5 1
0 98 121 2.5 1
1 99 123 2.5 2
```

The final weight of a state can be accessed using the `final` instance method.

```txt
>>> for state in f.states():
...     print state, f.final(state)
0 Infinity
1 Infinity
2 3.5
```

The following function can be used to count the number of arcs and states in an
FST.

```cpp
>>> def num_arcs_and_states(f):
...   return sum(1 + f.num_arcs(s) for s in f.states())
```

## FST mutation

Mutable FSTs can be modified by adding states (`add_state`), adding arcs leaving
existing states (`add_arc`), marking a existing state as the start state
(`set_start`), or giving a non-infinite final weight to an existing state
(`set_final`). Optionally, the user can reserve states before adding them using
the `reserve_states` instance method, and reserve arcs leaving an existing state
using the `reserve_arcs` method. The following snippet creates an acceptor
which, when its arc labels are interpreted as bytes, accepts the well-known
"sheep language" `/baa+/`.

```txt
>>> f = fst.VectorFst()
>>> one = fst.Weight.one(f.weight_type())
>>> f.reserve_states(3)  # Optional.
>>> s = f.add_state()
>>> f.set_start(s)
>>> n = f.add_state()
>>> f.reserve_arcs(s, 1)  # Optional.
>>> f.add_arc(s, fst.Arc(98, 98, one, n))
>>> s = n
>>> n = f.add_state()
>>> f.reserve_arcs(s, 1)  # Optional.
>>> f.add_arc(s, fst.Arc(97, 97, one, n))
>>> s = n
>>> n = f.add_state()
>>> f.reserve_arcs(s, 1)  # Optional.
>>> f.add_arc(s, fst.Arc(97, 97, one, n))
>>> f.reserve_arcs(n, 1)  # Optional.
>>> f.add_arc(n, fst.Arc(97, 97, one, n))
>>> f.set_final(n, one)
>>> f.verify()  # Checks FST's sanity.
True
>>> print f
0       1       98      98
1       2       97      97
2       3       97      97
3       3       97      97
3
```

While it is possible to add arcs whose destination state has not yet been added,
any other references to states not yet created (by `add_state`) is forbidden and
will raise an `FstIndexError`.

Existing arcs and states can also be deleted using `delete_states`, and arcs
leaving an existing state can be deleted using `delete_arcs`. For example, the
following function can be used to remove all arcs and states from an FST.

```txt
>>> def clear(f):
...   for state in f.states():
...       f.delete_arcs(state)
...   f.delete_states()
```

## FST visualization

The instance method text returns a string representing the FST as an arc-list
using the same format and options as the `fstprint` binary. If `f` is an FST,
then `print f` is an alias for `print f.text()`.

```txt
>>> print f
0       1       98      98
1       2       97      97
2       3       97      97
3       3       97      97
3
```

FSTs can also be written to a [GraphViz](http://graphviz.org) file using the
`draw` instance method.

```txt
>>> f.draw("f.gv")
```

## FST operations

All FSTs support constructive operations such as [composition](compose.md)
(`compose`), [intersection](intersect.md) (`intersect`), and
[reversal](reverse.md) (`reverse`), storing the result in a vector FST.

```txt
>>> cv = fst.compose(c, v)
```

FSTs also support tests for [equality](equal.md) (`equal`),
[equivalence](equivalent.md) (`equivalent`),
[stochastic equivalence](rand_equivalent.md) (`randequivalent`), and
[isomorphism](isomorphic.md) (`isomorphic`).

```txt
>>> fst.isomorphic(c, v)
True
```

FSTs which are [mutable](advanced_usage.md#fst-types) (e.g., `vector` FSTs) also
support destructive operations such as [arc-sorting](arc_sort.md) (`arcsort`),
[inversion](invert.md) (`invert`), [projection](project.md) (`project`), and
[union](union.md) (`union`). These operations work in place, mutating the
instance they are called on and returning nothing. These instance methods are
not available for immutable FST types (e.g., `const` FSTs).

```txt
>>> v.arcsort(sort_type="olabel")
>>> v.invert()
>>> v.project()
```

A few operations (e.g., weight-pushing, epsilon-removal) are available in both
constructive and destructive forms, albeit with slightly different options.

To read documentation on individual FST operations, use Python's built-in `help`
function.

```txt
>>> help(fst.equal)
Help on built-in function equal in module pywrapfst:

equal(...)
    equal(ifst1, ifst2, delta=fst.kDelta)

Are two FSTs equal?

    This function tests whether two FSTS have the same states with the same
    numbering and the same transitions with the same labels and weights in the
    same order.

    Args:
      ifst1: The first input FST.
      ifst2: The second input FST.
      delta: Comparison/quantization delta.

    Returns:
      True if the two FSTs satisfy the above conditions, otherwise False.

    See also: `equivalent`, `isomorphic`, `randequivalent`.
```

## FST output

FSTs can be written to disk using the `write` instance method.

```txt
>>> f.write("f.fst")
```

They also can be written into FARs using the `FarWriter` object. Once created,
an FST can be written to the `FarWriter` object using dictionary-style
assignment.

```txt
>>> writer = fst.FarWriter.create("lattice.far")
>>> writer["1best"] = 1best
>>> writer["2best] = 2best
```

Note that the FAR itself is not guaranteed to be flushed to disk until the
`FarWriter` is garbage-collected. Under normal circumstances, calling `del` on
the `FarWriter` variable will decrement the reference count to zero and trigger
garbage collection on the next cycle.

```txt
>>> del writer
```

## Worked example

Putting it all together, the following example, based on
[Mohri et al. 2002, 2008](background.md), shows the construction of an ASR
recognition transducer from a pronunciation lexicon *L*, grammar *G*, a
transducer from context-dependent phones to context-independent phones *C*, and
an HMM set *H* (where we assume that the components are all determinizable and,
preferably, in the log semiring).

```txt
>>> reader = fst.FarReader.open("hclg.far")
>>> LG = fst.determinize(fst.compose(reader["L"], reader["G"]))
>>> CLG = fst.determinize(fst.compose(reader["C"], LG))
>>> HCLG = fst.determinize(fst.compose(reader["H"], CLG))
>>> HCLG.minimize()
>>> HCLG.write("hclg.fst")
```
