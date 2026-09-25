# permutation_reconstruction_simple_swap

A preliminary research implementation for reconstructing an ($n\times n$) permutation matrix from partially observed row and column statistics using a simple swap-based search procedure.

The project explores a different formulation of permutation reconstruction from a regression-based approach as in github.com/algorithmic-andy/permutation_reconstruction. **The objective is no longer to predict solver performance, but to directly search for the hidden permutation.**

## Problem

Let

$$
X\in\mathbb{N}^{n\times n}
$$

be a matrix containing every integer from ($1$) through ($n^2$) exactly once.

For each row and column, we compute two invariant statistics:

* the sum;
* the logarithm of the product, equivalently the sum of logarithms.

Thus, for row ($i$),

$$
S_i^{(r)}=\sum_j X_{ij},
\qquad
L_i^{(r)}=\sum_j\log X_{ij},
$$

and analogously for each column.

Only a random subset of these statistics is observed. Each statistic is independently revealed with probability ($p$), initially taken to be ($0.5$).

No diagonal or anti-diagonal statistics are used.

## Canonicalization

The permutation space contains many equivalent representations under:

* row permutations;
* column permutations;
* transposition.

A canonical family is therefore introduced in which each matrix is lexicographically minimal under these transformations.

One useful consequence is that every canonical matrix has

$$
X_{11}=1.
$$

The search therefore operates over canonical representatives rather than treating equivalent matrices as distinct solutions.

## Simple Swap

The first search model uses two independently initialized hypotheses:

1. a **row hypothesis**, which attempts to satisfy the observed row sums and row log-sums;
2. a **column hypothesis**, which attempts to satisfy the observed column sums and column log-sums.

For the initial implementation,

$$
n=4,
$$

so each hypothesis is a ($4\times4$) permutation of ($1,\ldots,16$).

Each hypothesis maintains its own error with respect to the observed statistics.

A basic move consists of swapping two entries in one hypothesis while adhering to canonical family form.

The key idea is that the row and column hypotheses are initially independent, but should not remain independent. A simulated-annealing procedure gradually introduces a **time-dependent compatibility mechanism** between them.

At each step, the algorithm decides whether to propose a move in the row hypothesis or the column hypothesis according to their relative reconstruction errors.

The resulting process is intended to encourage the two independently constructed views of the permutation to progressively become compatible. Moves that negligibly affect the hypothesis error but greatly improve compatibility may be effective, especially near the termination of the algorithm as a form of local solution polishing.

## Research Direction

The simple-swap model is deliberately minimal.

It is intended to establish whether two independently optimized marginal representations can be coupled effectively enough to reconstruct a hidden permutation before introducing more sophisticated search mechanisms.

Possible future extensions include:

* richer swap operators;
* compatibility functions based on overlap between hypotheses;
* adaptive temperature schedules;
* alternative proposal distributions;
* multiple interacting hypotheses;
* learned proposal mechanisms;
* MCTS or other structured search procedures;
* compatibility metrics derived from decomposed reconstruction losses.

The goal of this repository is therefore not to present a finished reconstruction algorithm, but to establish a clean experimental foundation for studying **coupled statistical search over permutation spaces**.

## Status

**Preliminary research implementation.**

The initial objective is to determine whether the simple-swap mechanism provides a useful search signal at all. Performance, convergence behavior, and scaling should therefore be treated as empirical research questions rather than established properties of the method.

## Core Hypothesis

The central hypothesis is that independently optimizing row and column constraints provides two partially informative views of the same hidden permutation.

Rather than optimizing a single global objective from the beginning, the algorithm allows these views to evolve separately and introduces compatibility progressively through simulated annealing.

This creates a simple experimental setting for studying whether **independent partial hypotheses can be coupled into a globally consistent reconstruction.**
