---
title: "Structure of Stabiliser Codes"
weight: 10
math: true
---

<style>
  details.bb-derive {
    border: 1px solid rgba(234,88,12,0.2);
    border-radius: 8px;
    margin: 1.25rem 0;
    padding: 0;
    overflow: hidden;
  }
  details.bb-derive summary {
    cursor: pointer;
    padding: 0.7rem 1rem;
    font-size: 0.875rem;
    font-weight: 600;
    color: #ea580c;
    list-style: none;
    user-select: none;
    background: rgba(234,88,12,0.04);
  }
  details.bb-derive summary::-webkit-details-marker { display: none; }
  details.bb-derive summary::before {
    content: "▶ ";
    font-size: 0.7rem;
  }
  details.bb-derive[open] summary::before { content: "▼ "; }
  details.bb-derive .bb-derive-body {
    padding: 0.75rem 1.1rem 1rem;
    font-size: 0.9rem;
    line-height: 1.75;
    border-top: 1px solid rgba(234,88,12,0.12);
  }
</style>

The figure below shows the basic structure of an $[[n,k,d]]$ quantum stabilizer code. A register of $k$ data qubits, $|\psi\rangle_D$, is entangled with $m = n - k$ redundancy qubits $|0\rangle_R$ through an encoding operation to create a logical qubit $|\psi\rangle_L$. The data is now distributed across the expanded Hilbert space. Errors can then be detected by performing $m$ stabilizer measurements $P_i$ as shown in the figure.

![General structure of a stabiliser code](/images/stabiliser_structure.jpg)

*Circuit illustrating the structure of an $[[n, k, d]]$ stabilizer code. A quantum data register $|\psi\rangle_D = |\psi_1\psi_2...\psi_k\rangle$ is entangled with redundancy qubits $|0\rangle_R = |0_1 0_2...0_{n-k}\rangle$ via an encoding operation to create a logical qubit $|\psi\rangle_L$. After encoding, a sequence of $n - k$ stabilizer checks $P_i$ are performed on the register, and each result copied to an ancilla qubit $A_i$. The subsequent measurement of the ancilla qubits provides an $m$-bit syndrome.*

For each stabiliser, the syndrome extraction circuit maps the logical state as follows:

$$
E|\psi\rangle_L|0\rangle_{A_i}
\xrightarrow{\text{syndrome extraction}}
\frac{1}{2}(\mathbb{1}^{\otimes n}+P_i)E|\psi\rangle_L|0\rangle_{A_i}
+\frac{1}{2}(\mathbb{1}^{\otimes n}-P_i)E|\psi\rangle_L|1\rangle_{A_i}
$$

<details class="bb-derive">
<summary>Derivation of the syndrome extraction equation</summary>
<div class="bb-derive-body">

Starting from the initial state $E|\psi\rangle_L|0\rangle_{A_i}$, we trace through the syndrome extraction circuit step by step.

**Step 1 — Apply Hadamard to the ancilla:**

$$E|\psi\rangle_L|0\rangle_{A_i} \xrightarrow{H_{A_i}} E|\psi\rangle_L \otimes \frac{|0\rangle_{A_i} + |1\rangle_{A_i}}{\sqrt{2}}$$

**Step 2 — Apply controlled-$P_i$ (controlled on ancilla):**

The controlled-$P_i$ gate applies $P_i$ to the logical qubits when the ancilla is in state $|1\rangle$:

$$\frac{1}{\sqrt{2}}\bigl(E|\psi\rangle_L|0\rangle_{A_i} + P_i E|\psi\rangle_L|1\rangle_{A_i}\bigr)$$

**Step 3 — Apply Hadamard to the ancilla again:**

Using $H|0\rangle = \frac{1}{\sqrt{2}}(|0\rangle + |1\rangle)$ and $H|1\rangle = \frac{1}{\sqrt{2}}(|0\rangle - |1\rangle)$:

$$\frac{1}{2}(E|\psi\rangle_L + P_i E|\psi\rangle_L)|0\rangle_{A_i} + \frac{1}{2}(E|\psi\rangle_L - P_i E|\psi\rangle_L)|1\rangle_{A_i}$$

**Step 4 — Factor out the projectors:**

$$\frac{1}{2}(\mathbb{1}^{\otimes n}+P_i)E|\psi\rangle_L|0\rangle_{A_i}
+\frac{1}{2}(\mathbb{1}^{\otimes n}-P_i)E|\psi\rangle_L|1\rangle_{A_i} \quad \checkmark$$

</div>
</details>

From above, we see that if the stabilizer $P_i$ commutes with an error $E$, the measurement of ancilla qubit $A_i$ returns '0'. If the stabilizer $P_i$ anti-commutes with an error $E$, the measurement returns '1'.

To see why this is the case, recall that the logical state $|\psi\rangle_L$ lies in the $+1$ eigenspace of all stabilizers, meaning $P_i|\psi\rangle_L = |\psi\rangle_L$.

**Case 1: Error commutes with stabilizer** $[E, P_i] = 0$

When $E$ and $P_i$ commute, we have $P_i E = E P_i$. Therefore:

$$P_i E|\psi\rangle_L = E P_i|\psi\rangle_L = E|\psi\rangle_L$$

Substituting into the syndrome extraction result:

$$\frac{1}{2}(\mathbb{1}^{\otimes n}+P_i)E|\psi\rangle_L|0\rangle_{A_i}
+\frac{1}{2}(\mathbb{1}^{\otimes n}-P_i)E|\psi\rangle_L|1\rangle_{A_i}$$

$$= \frac{1}{2}(E|\psi\rangle_L + E|\psi\rangle_L)|0\rangle_{A_i} + \frac{1}{2}(E|\psi\rangle_L - E|\psi\rangle_L)|1\rangle_{A_i}$$

$$= E|\psi\rangle_L|0\rangle_{A_i}$$

The ancilla is deterministically in state $|0\rangle$, so measurement yields **'0'**.

**Case 2: Error anti-commutes with stabilizer** $\{E, P_i\} = 0$

When $E$ and $P_i$ anti-commute, we have $P_i E = -E P_i$. Therefore:

$$P_i E|\psi\rangle_L = -E P_i|\psi\rangle_L = -E|\psi\rangle_L$$

Substituting into the syndrome extraction result:

$$\frac{1}{2}(\mathbb{1}^{\otimes n}+P_i)E|\psi\rangle_L|0\rangle_{A_i}
+\frac{1}{2}(\mathbb{1}^{\otimes n}-P_i)E|\psi\rangle_L|1\rangle_{A_i}$$

$$= \frac{1}{2}(E|\psi\rangle_L - E|\psi\rangle_L)|0\rangle_{A_i} + \frac{1}{2}(E|\psi\rangle_L + E|\psi\rangle_L)|1\rangle_{A_i}$$

$$= E|\psi\rangle_L|1\rangle_{A_i}$$

The ancilla is deterministically in state $|1\rangle$, so measurement yields **'1'**.

The task of constructing a good code therefore involves finding stabilizers that anti-commute with the errors to be detected. The results of the $m$ stabilizer measurements are combined to give an $m$-bit syndrome. For a well-designed code, the syndrome allows us to deduce the best recovery operation to restore the logical state to the codespace.


## A general encoding circuit for stabilizer codes

In the quantum codes we have considered so far, we simply gave the encoding circuit for obtaining the logical state. Here, we will give a general method for preparing the logical states of a stabiliser code using the same circuits that are used for syndrome extraction.

The $|0\rangle_L$ for any stabiliser code can be obtained through a projection onto the $(+1)$ eigenspace of all its stabilizers:

$$|0\rangle_L = \frac{1}{N}\prod_{P_i\in\langle\mathcal{S}\rangle}(\mathbb{1}^{\otimes n}+P_i)|0^{\otimes n}\rangle$$

where $\langle\mathcal{S}\rangle$ is the minimal generating set of the code stabilizers and $1/N$ is a normalisation factor.

The $|0\rangle_L$ codeword of any stabilizer code can be prepared via this projection by applying the syndrome extraction circuit to a $|0\rangle^{\otimes n}$ state. As an example, consider preparing $|0\rangle_L$ of Shor's nine-qubit code by applying the syndrome extraction circuit to the state $|0\rangle^{\otimes 9}$. The stabilizer group of Shor's code has eight generators: six Z-type stabilizers ($Z_1Z_2$, $Z_2Z_3$, $Z_4Z_5$, $Z_5Z_6$, $Z_7Z_8$, $Z_8Z_9$) and two X-type stabilizers ($X_1X_2X_3X_4X_5X_6$, $X_4X_5X_6X_7X_8X_9$).

For the Z-type stabilizers, the initial state $|0\rangle^{\otimes 9}$ is already in the $(+1)$ eigenspace. For example, $Z_1Z_2|0\rangle^{\otimes 9} = (+1)(+1)|0\rangle^{\otimes 9} = |0\rangle^{\otimes 9}$, so the ancilla measurement deterministically yields '0'.

However, for the X-type stabilizers, the initial state is not an eigenstate. Consider the extraction of $X_1X_2X_3X_4X_5X_6$. Since $X|0\rangle = |1\rangle$, we have:

$$X_1X_2X_3X_4X_5X_6|000000000\rangle = |111111000\rangle$$

The intermediary state after syndrome extraction is:

$$\frac{1}{2}(\mathbb{1} + X_1X_2X_3X_4X_5X_6)|000000000\rangle|0\rangle_A + \frac{1}{2}(\mathbb{1} - X_1X_2X_3X_4X_5X_6)|000000000\rangle|1\rangle_A$$

$$= \frac{1}{2}(|000000000\rangle + |111111000\rangle)|0\rangle_A + \frac{1}{2}(|000000000\rangle - |111111000\rangle)|1\rangle_A$$

When the ancilla is measured, the state collapses to either the $(+1)$ or $(-1)$ projection with equal probability. If the '1' syndrome is measured, a correction (such as applying $X_1$) must be applied to transform the state back onto the $(+1)$ eigenspace of the stabilizer. Repeating this procedure for the remaining X-type stabilizer will lead to the preparation of the $|0\rangle_L$ codeword for Shor's code.


## Code Space Dimension

Given a stabiliser code defined by a list of n-qubit stabiliser generators $P_1, \ldots, P_r$, what is the dimension of the code space that it defines?

The answer is that the dimension of the codespace must be $2^{n-r}$, so $n-r$ qubits can be encoded using this code.

For example, the 3-bit repetition code for detecting bit-flip errors has $n = 3$ and $2$ stabiliser generators, and as such it only encodes a single qubit.

<details class="bb-derive">
<summary>Proof of code space dimension</summary>
<div class="bb-derive-body">

**Step 1:** Every stabilizer element is a product of a subset of generators. Because the generators commute and each Pauli is its own inverse, any element of the stabilizer can be written as $P_1^{a_1} \cdots P_r^{a_r}$ where each exponent $a_k$ is either 0 or 1. Each element corresponds to exactly one such subset, so there are exactly $2^r$ elements in the stabilizer.

**Step 2:** For each generator $P_k$, define a projection onto its +1 eigenspace:

$$\Pi_k = \frac{I^{\otimes n} + P_k}{2}$$

This operator projects onto the +1 eigenvectors of $P_k$.

**Step 3:** The product of these projections gives the projection onto the code space. Since the generators commute, so do the projections, and multiplying them gives the projection onto the intersection of all +1 eigenspaces:

$$\Pi_1 \cdots \Pi_r = \text{projection onto the code space } C$$

**Step 4:** Expanding the product $\left(\frac{I + P_1}{2}\right) \cdots \left(\frac{I + P_r}{2}\right)$ gives:

$$\Pi_1 \cdots \Pi_r = \frac{1}{2^r} \sum_{a_1, \ldots, a_r \in \{0,1\}} P_1^{a_1} \cdots P_r^{a_r}$$

Every term in that sum is one of the stabilizer elements from Step 1. The projection onto the code space is the average of all elements of the stabilizer.

**Step 5:** The dimension of any subspace equals the trace of the projection onto it:

$$\dim(C) = \text{Tr}(\Pi_1 \cdots \Pi_r) = \frac{1}{2^r} \sum_{a_1, \ldots, a_r} \text{Tr}(P_1^{a_1} \cdots P_r^{a_r})$$

There are two cases:
- The all-zeros term ($a_1 = \cdots = a_r = 0$) is $I^{\otimes n}$, whose trace is $2^n$.
- Every other term is a non-identity Pauli operation. The product can't equal $+I^{\otimes n}$ (that would make the generators non-minimal) and can't equal $-I^{\otimes n}$ (the stabilizer condition forbids it). Every non-identity Pauli has trace zero, so all these terms vanish.

Only the first term survives:

$$\dim(C) = \frac{1}{2^r} \cdot 2^n = 2^{n-r}$$

**Remark:** This proof justifies the requirement that $-I^{\otimes n}$ is not in the stabilizer. This condition guarantees the code space contains a state, since the dimension $2^{n-r}$ is never zero. If $-I^{\otimes n}$ were in the stabilizer, no nonzero vector could be fixed by it, and the code space would be empty.

</div>
</details>
