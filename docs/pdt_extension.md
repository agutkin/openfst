# Pushdown Transducer Library (PDTs)

This is a push-down transducer (PDT) extension of the
[OpenFst](https://www.openfst.org) library. A PDT is encoded as an FST, where
some transitions are labeled with open or close parentheses. To be interpreted
as a PDT, the parentheses must balance on a path.

configure      | include
-------------- | -------------------------------
`--enable-pdt` | `<fst/extensions/pdt/pdtlib.h>`

A PDT is encoded as an FST where some transitions are labeled with open or close
parentheses. To be interpreted as a PDT, the parentheses must balance on a path.
The subset of the transducer labels that correspond to parenthesis (open,
closed) pairs is designated from the C++-library in a `vector<pair<Label,
Label>>` and from the command line in a file of lines of label pairs (typically
passed with the flag `--pdt_parentheses`). See Cyril Allauzen and Michael Riley,
["A Pushdown Transducer Extension for the OpenFst Library"](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/40342.pdf),
*Proceedings of the Seventeenth International Conference on Implementation and
Application of Automata*, (CIAA 2012)

The following operations, many which have FST analogues (but are distinguished
in C++ by having a `vector<pair<Label, Label>>` parenthesis pair argument), are
provided for PDTs:

Operation    | Usage                                                                                           | Description                                                            | Complexity
------------ | ----------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | ----------
Compose      | `Compose(a_pdt, parens, b_fst, &c_pdt);`                                                        | compose a PDT and an FST with PDT result (Bar-Hillel)                  | Same as FST [composition](compose.md)
&nbsp;       | `Compose(a_fst, b_pdt, parens, &c_pdt);`                                                        |                                                                        |
&nbsp;       | `pdtcompose -pdt_parentheses=pdt.parens a.pdt b.fst >c.pdt`                                     |                                                                        |
&nbsp;       | `pdtcompose -pdt_parentheses=pdt.parens -pdt_left_pdt=false a.fst b.pdt >c.pdt`                 |                                                                        |
Expand       | `Expand(a_pdt, parens, &b_fst);`                                                                | expands a (bounded-stack) PDT as an FST                                | Time, Space: $O(e^{O(V + E)})$
&nbsp;       | `pdtexpand -pdt_parentheses=pdt.parens a.pdt >b.fst`                                            |                                                                        |
Info         | `pdtinfo -pdt_parentheses=pdt.parens a.pdt`                                                     | prints out information about a PDT                                     |
Replace      | `Replace(fst_label_pairs, &b_pdt, root_label, &parens);`                                        | Converts an RTN represented by FSTs and non-terminal labels into a PDT | Time, Space: $O(\sum (V_i + E_i))$
&nbsp;       | `pdtreplace -pdt_parentheses=pdt.parens root.fst rootlabel [rule1.fst rule1.label ...] out.pdt` |                                                                        |
Reverse      | `Reverse(a_pdt, parens, &b_pdt);`                                                               | reverses a PDT                                                         | Time, Space: $O(V + E)$
&nbsp;       | `pdtreverse -pdt_parentheses=pdt.parens a.pdt >b.pdt`                                           |                                                                        |
ShortestPath | `ShortestPath(a_pdt, parens, &b_fst);`                                                          | find the shortest path in a (bounded-stack) PDT (cf. Earley)           | Time: $O((V + E)^3)$, Space: $O((V + E)^2)$
&nbsp;       | `pdtshortestpath -pdt_parentheses=pdt.parens a.pdt >b.fst`                                      |                                                                        |

There are also delayed versions of these algorithms where possible. See the
header files for additional information including options. Note with this
FST-based representation of PDTs, many FST operations (e.g., `Concat`,
`Closure`, `Rmepsilon`, `Union`) work equally well on PDTs as FSTs.

As an example of this representation and these algorithms, the transducer in
textual format:

```bash
fstcompile >pdt.fst <<EOF
0 1 1 1
1 0 3 3
0 2 0 0
2 3 2 2
3 2 4 4
2
EOF
```

with parentheses:

```bash
cat >pdt.parens <<EOF
3 4
EOF
```

accepts `1n2n`.

This can be shown with:

```bash
$ fstcompile >1122.fst <<EOF
0 1 1 1
1 2 1 1
2 3 2 2
3 4 2 2
4
EOF

# intersect the FST and PDT; the result is a PDT
pdtcompose --pdt_parentheses=pdt.parens pdt.fst 1122.fst |
# expand the (bounded-stack) PDT into an FST; this enforces the parenthesis matching
pdtexpand --pdt_parentheses=pdt.parens |
# remove epsilons (formerly the parentheses)
fstrmepsilon | fstprint
0   1   1   1
1   2   1   1
2   3   2   2
3   4   2   2
4
```

Had the input string been `112`, the result of the composition would be a
non-empty PDT representing a path with unbalanced parentheses. The following
expansion step would then result in an empty FST.

The above recognition algorithm has the exponential complexity of conventional
PDT parsing. An alternate approach with cubic complexity, which is a
generaliztion of Earley's algorihtm, is:

```bash
# intersect the FST and PDT; the result is a PDT
pdtcompose --pdt_parentheses=pdt.parens pdt.fst 1122.fst |
# find the shortest balanced path
pdtshorestpath --pdt_parentheses=pdt.parens |
# remove epsilons (formerly the parentheses)
fstrmepsilon | fstprint
0   1   1   1
1   2   1   1
2   3   2   2
3   4   2   2
4
```

Finally, the following invocation returns all paths within `$threshold` of the
best accepted path as an FST (cf. [Prune](prune.md)). The algorithm has cubic
complexity when the threshold is near 0.0 (dominated by a shortest distance
computation) and becomes exponential as it approaches infinity (dominated by the
expansion operation):

```bash
# intersect the FST and PDT; the result is a PDT
pdtcompose --pdt_parentheses=pdt.parens pdt.fst in.fst |
# expand the (bounded-stack) PDT into an FST keeping paths within a threshold of the best path
pdtexpand --weight=$threshold ---pdt_parentheses=pdt.parens >out.fst
```
