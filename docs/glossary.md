# OpenFst Glossary

acceptor {#acceptor}
:   An acceptor is a finite automaton where each transition has a label and
    possibly a weight. In this library, an acceptor is represented as a
    transducer with the input and output label of each transition being equal.

accessible {#accessible}
:   A state **q** is accessible iff there is a path from the initial state to
    **q**.

co-accessible {#co-accessible}
:   A state **q** is co-accessible iff there is a path from **q** to a final
    state.

delayed {#delayed}
:   An FST is delayed (or *lazy*, *on-the-fly*, or *dynamic*) if the computation
    of its states and transitions occur only when requested. The complexity of a
    delayed FSTs constructor is constant-time, while the complexity of its
    traversal is a function of only those states and transitions that are
    visited.

deterministic {#deterministic}
:   An acceptor is deterministic iff for each state **q** there is at most one
    transition labeled with a given label. A transducer can be input
    deterministic (or *subsequential*) or output deterministic.

epsilon, ε {#epsilon}
:   An input label that consumes no input or an output label emits no output.

equivalent {#equivalent}
:   Two transducers are equivalent if for each input string, they produce the
    same output strings with the same weight. The output strings, however, may
    differ in the number of paths, in the location of epsilon transitions, or in
    the distribution of the weights along the paths.

functional {#functional}
:   A transducer is functional if each input string is transduced to a unique
    output string. There may be multiple paths, however, that contain this input
    and output string pair.

multiplicity {#multiplicity}
:   The maximum number of times a label is repeated at a state - a measure of
    [non-determinism](glossary.md#deterministic).

out-degree {#out-degree}
:   The number of transitions exiting a state.

semiring {#semiring}
:   A semiring is a set of elements (*weights*) together with two binary
    operations $\oplus$ and $\otimes$ and two designated elements $0$ and
    $1$ with the following properties:

    *   $\oplus$: associative, commutative, and has $0$ as its identity.
    *   $\otimes$: associative and has identity $1$, distributes w.r.t.
        $\oplus$, and has $0$ as an annihilator: $0 \otimes a = a \otimes
        0 = 0$.

successful path {#successful-path}
:   A path from the initial state to some final state.

transducer {#transducer}
:   A transducer is a finite automaton where each transition has an input label,
    an output label, and possibly a weight.

trim {#trim}
:   An automaton is trim (or *connected*) iff it contains no
    [inaccessible](glossary.md#accessible) or
    [no-coaccessible](glossary.md#co-accessible) states.

unambiguous {#unambiguous}
:   A transducer is unambiguous iff it has at most one successful path for any
    input labeling.

zero-sum-free {#zero-sum-free}
:   A semiring is zero-sum-free if $\forall a,b: a \oplus b = 0 \Rightarrow a =
    b = 0$.
