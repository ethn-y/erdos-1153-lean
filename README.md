# Erdős Problem 1153 in Lean

This repository gives a self-contained Lean 4 proof of the affirmative answer
to Erdős problem 1153. For distinct interpolation nodes
$x_1,\ldots,x_n\in[-1,1]$, let

```math
\ell_k(x)=\prod_{i\ne k}\frac{x-x_i}{x_k-x_i},
\qquad
\lambda(x)=\sum_k |\ell_k(x)|.
```

For every fixed nondegenerate interval $[a,b]\subseteq[-1,1]$, the proof
establishes, uniformly over all choices of the nodes,

```math
\max_{x\in[a,b]}\lambda(x)>
\left(\frac{2}{\pi}-o(1)\right)\log n.
```

The human-readable argument is in [PROOF.md](PROOF.md). The
[statement correspondence table](STATEMENT-CORRESPONDENCE.md) maps every
source clause and quantifier to Lean and identifies the few interpretation
choices that kernel checking cannot settle. The exact formal target is
[Erdos1153/Statement.lean](Erdos1153/Statement.lean), and the final theorem is
[Erdos1153/Main.lean](Erdos1153/Main.lean):

```lean
theorem Erdos1153.erdos1153_main : Erdos1153.Target
```

The proof development has 69 Lean source files and 26,525 lines. It uses Lean
4.27.0 and Mathlib 4.27.0, both pinned in the repository.

## Verify it

Install [elan](https://github.com/leanprover/elan), then run:

```bash
lake exe cache get
./verify.sh
```

The verification script:

1. builds the complete project;
2. compiles [StatementAudit.lean](StatementAudit.lean), including the checked
   equivalence between the displayed maximum and the public witness target;
3. checks the final theorem directly;
4. rejects proof placeholders and project-defined axioms;
5. confirms that the proof does not import an external benchmark theorem; and
6. prints the kernel axiom report for the final theorem.

The expected axiom surface is:

```text
propext
Classical.choice
Quot.sound
```

These are standard Lean/Mathlib foundations. There is no sorry axiom and no
project-defined axiom. GitHub Actions runs the same checks on every push and
pull request.

## What is formalized

The proof follows the route in [PROOF.md](PROOF.md):

| Argument stage | Lean modules |
|---|---|
| Statement, Lagrange interpolation, ordering, and compact maxima | [Statement.lean](Erdos1153/Statement.lean), [NodeOrder.lean](Erdos1153/NodeOrder.lean), [Interpolation.lean](Erdos1153/Interpolation.lean), [CompactMax.lean](Erdos1153/CompactMax.lean) |
| Sharp full-interval lower bound via clipped pair energy | [ClassicalBound/](Erdos1153/ClassicalBound/) |
| de Boor--Pinkus comparison theorem | [DeBoorPinkus/](Erdos1153/DeBoorPinkus/) |
| Consecutive-block collapse and localization | [Collapse/](Erdos1153/Collapse/) |
| Fixed-interval damping and sparse/dense dichotomy | [Density/](Erdos1153/Density/) |
| Ordered and arbitrary-node assembly | [OrderedStatement.lean](Erdos1153/OrderedStatement.lean), [Main.lean](Erdos1153/Main.lean) |

The de Boor--Pinkus theorem and the sharp full-interval estimate are proved
inside this repository; neither is introduced as an assumption.

## Statement boundary

There was no public Google DeepMind Formal Conjectures declaration for problem
1153 when this package was completed. The public review surface is therefore
the [clause-by-clause correspondence table](STATEMENT-CORRESPONDENCE.md) and
the compiled [StatementAudit.lean](StatementAudit.lean). Together they expose
the exact quantifiers and definitions, prove the literal Lagrange-product
bridges, and prove that the displayed maximum formulation is equivalent to the
public witness formulation.

This is deliberately a narrower statement-certification claim than 825's
external adapter. Lean checks that the proof establishes the frozen
proposition and that all formula/maximum correspondences are exact;
the table exposes, rather than conceals, the remaining human source-reading
boundary.

The proof depends only on Mathlib and does not import any upstream Erdős 1153
theorem. The original problem is recorded at
[erdosproblems.com/1153](https://www.erdosproblems.com/1153).

## Mathematical source

The interpolation comparison theorem reconstructed in the formal proof is:

Carl de Boor and Allan Pinkus, “Proof of the Conjectures of Bernstein and
Erdős Concerning the Optimal Nodes for Polynomial Interpolation,” *Journal of
Approximation Theory* 24 (1978), 289–303.

The sharp full-interval estimate is proved independently in Lean through
finite Riesz interpolation, Chebyshev expansion, clipped arcsine pair energy,
geometric bins, and harmonic spacing estimates.

## License

Copyright 2026 Ethan Yang. Released under the [MIT License](LICENSE).
