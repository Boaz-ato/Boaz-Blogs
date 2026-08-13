---
title: "Stabiliser Formalism"
weight: 9
math: true
---

So far, we have looked at the 3-qubit bit-flip code, 3-qubit phase code, and Shor's code. In all of these codes, we measured the eigenvalue of some Pauli operators and used the syndrome to determine the type of error that occurred. Here, we will formalise this concept using the stabiliser formalism.

## The Pauli Group
Below are the Pauli matrices for single qubits:
$$
I =
\begin{pmatrix}
1 & 0 \\
0 & 1
\end{pmatrix},
\qquad
X =
\begin{pmatrix}
0 & 1 \\
1 & 0
\end{pmatrix},
\qquad
Y =
\begin{pmatrix}
0 & -i \\
i & 0
\end{pmatrix},
\qquad
Z =
\begin{pmatrix}
1 & 0 \\
0 & -1
\end{pmatrix}
$$
We define the Pauli group $P_n$ on $n$ qubits as the set of all tensor products of the Pauli matrices $X$, $Y$, $Z$, and $I$, together with an overall phase of $\pm 1$ or $\pm i$. Any pair of elements in the Pauli group either commutes ($MN = NM$) or anticommutes ($MN = -NM$).

For instance, in the one-qubit case, the Pauli group consists of the elements $\{\pm I, \pm iI, \pm X, \pm iX, \pm Y, \pm iY, \pm Z, \pm iZ\}$. For this group, the identity commutes with every element and every element commutes with itself. It has the following anti-commutation relations:

$$XY = -YX  \qquad       XZ = -ZX  \qquad      YZ = -ZY$$ 

Elements of the Pauli group satisfy:

$$M^2 = \pm I.$$

We define the **weight** of $M \in P_n$ as the number of qubits on which $M$ acts as a non-identity operator.

## The Stabiliser Group

The Pauli operators can be used to generate a group called the **stabiliser** of a quantum code. An operator $M$ stabilises a state $|\psi\rangle$ if:

$$M|\psi\rangle = |\psi\rangle \quad \text{for all encoded states } |\psi\rangle.$$

The generators of the stabiliser group must be **minimal**: it must not be possible to obtain any generator $G_i$ as a product of the other generators $G_j$. For example, consider the set $S = \{Z_1Z_2,\, Z_2Z_3,\, Z_1Z_3\}$. This is not minimal, since $Z_1Z_3 = (Z_1Z_2)(Z_2Z_3)$. A minimal generating set is $S = \{Z_1Z_2,\, Z_2Z_3\}$.

Note also that the stabiliser set is closed under multiplication: if $M|\psi\rangle = |\psi\rangle$ and $N|\psi\rangle = |\psi\rangle$, then:

$$MN|\psi\rangle = |\psi\rangle.$$

So products of stabilisers are also stabilisers.

### The Abelian Condition

The stabiliser group must be **Abelian**. That is, every pair of elements must commute. This is necessary so that all stabilisers can be measured simultaneously, yielding a well-defined syndrome without the measurement order affecting the outcome.

**Proof.** Suppose $M, N \in S$, so $M|\psi\rangle = |\psi\rangle$ and $N|\psi\rangle = |\psi\rangle$. Consider the commutator acting on a codeword:

$$(MN - NM)|\psi\rangle = MN|\psi\rangle - NM|\psi\rangle = M|\psi\rangle - N|\psi\rangle = |\psi\rangle - |\psi\rangle = 0.$$

Since $M$ and $N$ are Pauli operators, they must either commute ($MN = NM$) or anticommute ($MN = -NM$). If they anticommuted, we would have:

$$(MN - NM)|\psi\rangle = MN|\psi\rangle - NM|\psi\rangle = |\psi\rangle -(-|\psi\rangle) = 2|\psi\rangle \neq 0,$$

which is a contradiction. Therefore $MN = NM$, and all elements of the stabiliser group must commute. $\square$

## The Codespace

Given an Abelian subgroup $S$ of $P_n$, we define the **codespace**:

$$T(S) = \left\{ |\psi\rangle \;\middle|\; M|\psi\rangle = |\psi\rangle \;\; \forall M \in S \right\}.$$

The codespace is a subspace of the full $2^n$-dimensional Hilbert space. Suppose we wish to encode $k$ logical qubits into $n$ physical qubits; then the stabiliser group $S$ has $n - k$ independent generators $G_1, \ldots, G_{n-k}$. Each generator has eigenvalues $\pm 1$, and measuring it splits the Hilbert space into two equal halves: the $+1$ eigenspace and the $-1$ eigenspace. Each successive generator measurement halves the remaining subspace, so after all $n - k$ measurements the joint $+1$ eigenspace has dimension $2^n / 2^{n-k} = 2^k$. This $2^k$-dimensional subspace is the codespace $T(S)$, encoding exactly $k$ logical qubits.

The syndrome — the list of $\pm 1$ eigenvalues obtained from measuring each generator — identifies which subspace the state lies in. An error is detected whenever any generator returns $-1$, signalling that the state has been pushed out of the codespace.

## Error Detection

Suppose $M \in S$ and a Pauli error $E$ **anticommutes** with $M$. Then:

$$M(E|\psi\rangle) = -EM|\psi\rangle = -E|\psi\rangle,$$

so $E|\psi\rangle$ has eigenvalue $-1$ for $M$. Conversely, if $E$ **commutes** with every $M \in S$:

$$M(E|\psi\rangle) = EM|\psi\rangle = E|\psi\rangle,$$

so $E|\psi\rangle$ has eigenvalue $+1$ for all stabilisers. The eigenvalue of $M$ therefore detects errors which anticommute with $M$.

## The Normaliser and Code Distance

Define the **normaliser** of $S$:

$$N(S) = \left\{ N \in P_n \;\middle|\; MN = NM \;\; \forall M \in S \right\}.$$

The **distance** $d$ of the code $T(S)$ is the minimum weight of any Pauli operator in $N(S) \setminus S$.

- Errors **not in** $N(S)$ are detected, as they anticommute with at least one stabiliser.
- Errors **in** $S$ leave all codewords unchanged and are called **degenerate errors** — they require no correction.
- Errors **in** $N(S) \setminus S$ commute with all stabilisers but act non-trivially on the codespace; these are the **logical operators** and cannot be detected.

To correct $t$ errors, the code must have distance $d \geq 2t + 1$.

Two errors $E$ and $F$ produce the same error syndrome if and only if $E^\dagger F \in N(S)$. If $E^\dagger F \notin N(S)$, the syndrome distinguishes them. If $E^\dagger F \in S$, the errors act identically on all codewords and there is no need to distinguish them.

## Summary

The stabilisers $\{G_i\}$ of an $[\![n, k, d]\!]$ code must satisfy the following properties:

1. **Pauli group elements:** Each $G_i \in P_n$.
2. **Stabilise all logical states:** $G_i |\psi\rangle_L = |\psi\rangle_L$ for all logical codewords $|\psi\rangle_L$.
3. **Mutual commutativity:** $[G_i, G_j] = 0$ for all $i, j$, so stabilisers can be measured simultaneously.

The stabiliser group $S = \langle G_1, \ldots, G_{n-k} \rangle$ is an Abelian subgroup of $P_n$. The code corrects any error set $\mathcal{E}$ such that $E^\dagger F \notin N(S) \setminus S$ for all pairs $E, F \in \mathcal{E}$.
