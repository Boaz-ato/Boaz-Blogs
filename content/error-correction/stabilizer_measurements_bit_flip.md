---
title: "Stabilizer Measurements: Detecting Bit-Flip Errors"
weight: 6
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

So far, we have established that bit-flip errors rotate the encoded qubit from the codespace into the error subspace, and that we can use projective measurements to identify which subspace the encoded qubit occupies. The natural question becomes: how do we actually perform these projective measurements?

## The Two-Qubit Code

To begin, let's consider the simplest encoded state, which uses two physical qubits:

$$|\psi\rangle = \alpha |0\rangle + \beta |1\rangle \xrightarrow{\text{encode}} |\psi\rangle_L = \alpha |00\rangle + \beta |11\rangle$$

The circuit below produces this encoding:

![Encoding circuit using two qubits](/images/two_syndrome.png)

The projective measurement is performed by measuring the operator $Z_1 Z_2$. Acting on the logical state:

$$Z_1 Z_2 |\psi\rangle_L = Z_1 Z_2 (\alpha |00\rangle + \beta |11\rangle) = (+1)|\psi\rangle_L$$

The $Z_1 Z_2$ operator **stabilises** the logical state — it leaves it unchanged and returns eigenvalue $+1$. Now consider the action of this operator on a corrupted state. Suppose the first qubit has suffered an $X$ error:

$$Z_1 Z_2 X_1|\psi\rangle_L = Z_1 Z_2 (\alpha |10\rangle + \beta |01\rangle) = (-1)|\psi\rangle_L$$

The corrupted state is the $-1$ eigenstate of $Z_1 Z_2$. The sign of the eigenvalue tells us whether an error has occurred.

## Syndrome Extraction Circuit

The circuit below shows the full process: encoding, applying a bit-flip error, and extracting the syndrome.

![Encoding and syndrome measurement for two qubits](/images/two_qubits_syndrome.png)

In the encode stage, a CNOT gate entangles $|\psi\rangle$ with a redundancy qubit to create the logical state $|\psi\rangle_L$. A bit-flip error $E$ is then applied. An ancilla qubit $|0\rangle_A$ is introduced to measure the $Z_1 Z_2$ stabiliser. The syndrome extraction stage transforms the state as follows:

$$E |\psi\rangle_L |0\rangle_A \xrightarrow{\text{syndrome extraction}} \frac{1}{2}(\mathbb{1} + Z_1 Z_2)\, E |\psi\rangle_L |0\rangle_A + \frac{1}{2}(\mathbb{1} - Z_1 Z_2)\, E |\psi\rangle_L |1\rangle_A$$

<details class="bb-derive">
<summary>Step-by-step derivation of the syndrome extraction equation</summary>
<div class="bb-derive-body">

**Step 1 — Prepare the ancilla and apply a Hadamard:**

$$E|\psi\rangle_L \otimes |0\rangle_A \xrightarrow{H_A} E|\psi\rangle_L \otimes \frac{|0\rangle_A + |1\rangle_A}{\sqrt{2}}$$

**Step 2 — Apply controlled-$Z_1 Z_2$ (ancilla controls $Z_1 Z_2$ on the data qubits):**

When the ancilla is $|0\rangle$, do nothing. When it is $|1\rangle$, apply $Z_1 Z_2$:

$$\frac{1}{\sqrt{2}}\bigl(E|\psi\rangle_L |0\rangle_A + Z_1 Z_2\, E|\psi\rangle_L |1\rangle_A\bigr)$$

**Step 3 — Apply a second Hadamard to the ancilla:**

$$\frac{1}{\sqrt{2}}\left(E|\psi\rangle_L \cdot \frac{|0\rangle+|1\rangle}{\sqrt{2}} + Z_1Z_2\,E|\psi\rangle_L \cdot \frac{|0\rangle-|1\rangle}{\sqrt{2}}\right)$$

Expanding and collecting terms by ancilla state:

$$= \frac{1}{2}(E|\psi\rangle_L + Z_1Z_2\,E|\psi\rangle_L)|0\rangle_A + \frac{1}{2}(E|\psi\rangle_L - Z_1Z_2\,E|\psi\rangle_L)|1\rangle_A$$

$$= \frac{1}{2}(\mathbb{1} + Z_1Z_2)\,E|\psi\rangle_L|0\rangle_A + \frac{1}{2}(\mathbb{1} - Z_1Z_2)\,E|\psi\rangle_L|1\rangle_A \quad \checkmark$$

</div>
</details>

Here, the error $E$ is drawn from the set $\{I, X_1, X_2, X_1 X_2\}$.

Now consider the case $E = X_1$, so that the logical state occupies the error subspace. We can show that the first term in the equation above vanishes:

<details class="bb-derive">
<summary>Step-by-step derivation for E = X₁</summary>
<div class="bb-derive-body">

The corrupted state is:

$$X_1|\psi\rangle_L = \alpha|10\rangle + \beta|01\rangle$$

**Evaluate $Z_1 Z_2$ acting on the corrupted state:**

$$Z_1 Z_2 (\alpha|10\rangle + \beta|01\rangle) = \alpha\underbrace{Z_1|1\rangle}_{-|1\rangle}\underbrace{Z_2|0\rangle}_{+|0\rangle} + \beta\underbrace{Z_1|0\rangle}_{+|0\rangle}\underbrace{Z_2|1\rangle}_{-|1\rangle}$$

$$= -\alpha|10\rangle - \beta|01\rangle = -X_1|\psi\rangle_L$$

So $X_1|\psi\rangle_L$ is a $-1$ eigenstate of $Z_1Z_2$.

**The first term vanishes:**

$$\frac{1}{2}(\mathbb{1} + Z_1Z_2)\,X_1|\psi\rangle_L = \frac{1}{2}(X_1|\psi\rangle_L + (-X_1|\psi\rangle_L)) = 0$$

**Only the second term survives:**

$$\frac{1}{2}(\mathbb{1} - Z_1Z_2)\,X_1|\psi\rangle_L|1\rangle_A = \frac{1}{2} \cdot 2\,X_1|\psi\rangle_L|1\rangle_A = X_1|\psi\rangle_L|1\rangle_A$$

The ancilla is measured deterministically as $1$, signalling an error. $\checkmark$

</div>
</details>

Considering all error patterns: if the logical state is in the codespace (i.e. $E \in \{I, X_1 X_2\}$) the ancilla is measured as $0$. If the logical state is in the error subspace (i.e. $E \in \{X_1, X_2\}$) the ancilla is measured as $1$. The outcome of the ancilla measurement is the **syndrome**.

However, the two-qubit code only tells us whether an error occurred — it cannot tell us *which* qubit was affected. It is therefore a detection code. To locate the error, we need multiple stabiliser measurements.

## The Three-Qubit Code

The three-qubit code is the natural extension: the encoding distributes the logical information across an entangled three-qubit state,

$$|\psi\rangle_L = \alpha|000\rangle + \beta|111\rangle$$

which lives in an eight-dimensional Hilbert space $\mathcal{H}_8$ with codespace $\mathcal{C} = \text{span}\{|000\rangle, |111\rangle\}$.

To distinguish between the four possible single-qubit error subspaces, we perform two stabiliser measurements, $Z_1 Z_2$ and $Z_2 Z_3$, using the circuit shown below.

![Encoding and syndrome measurement for the three-qubit logical state](/images/three_qubits_syndrome.png)

Both $Z_1 Z_2$ and $Z_2 Z_3$ commute with the logical state (they each return $+1$ on any codeword). Applying both extraction circuits in sequence gives four branches, one for each pair of ancilla outcomes:

$$E |\psi\rangle_L |0\rangle_{A_1}|0\rangle_{A_2} \longrightarrow$$

$$\frac{1}{4}(\mathbb{1} + Z_1 Z_2)(\mathbb{1} + Z_2 Z_3)\,E|\psi\rangle_L|0\rangle_{A_1}|0\rangle_{A_2}$$
$$+\; \frac{1}{4}(\mathbb{1} + Z_1 Z_2)(\mathbb{1} - Z_2 Z_3)\,E|\psi\rangle_L|0\rangle_{A_1}|1\rangle_{A_2}$$
$$+\; \frac{1}{4}(\mathbb{1} - Z_1 Z_2)(\mathbb{1} + Z_2 Z_3)\,E|\psi\rangle_L|1\rangle_{A_1}|0\rangle_{A_2}$$
$$+\; \frac{1}{4}(\mathbb{1} - Z_1 Z_2)(\mathbb{1} - Z_2 Z_3)\,E|\psi\rangle_L|1\rangle_{A_1}|1\rangle_{A_2}$$

<details class="bb-derive">
<summary>Step-by-step derivation of the four branches</summary>
<div class="bb-derive-body">

We apply the two-qubit syndrome extraction procedure twice in sequence.

**After the first extraction ($Z_1 Z_2$):**

$$E|\psi\rangle_L|0\rangle_{A_1}|0\rangle_{A_2} \longrightarrow \frac{1}{2}(\mathbb{1}+Z_1Z_2)\,E|\psi\rangle_L|0\rangle_{A_1}|0\rangle_{A_2} + \frac{1}{2}(\mathbb{1}-Z_1Z_2)\,E|\psi\rangle_L|1\rangle_{A_1}|0\rangle_{A_2}$$

**After the second extraction ($Z_2 Z_3$), each term splits again:**

For the first term ($A_1 = 0$):

$$\frac{1}{2}(\mathbb{1}+Z_1Z_2)\,E|\psi\rangle_L|0\rangle_{A_1} \longrightarrow$$
$$\frac{1}{4}(\mathbb{1}+Z_1Z_2)(\mathbb{1}+Z_2Z_3)\,E|\psi\rangle_L|0\rangle_{A_1}|0\rangle_{A_2} + \frac{1}{4}(\mathbb{1}+Z_1Z_2)(\mathbb{1}-Z_2Z_3)\,E|\psi\rangle_L|0\rangle_{A_1}|1\rangle_{A_2}$$

For the second term ($A_1 = 1$):

$$\frac{1}{2}(\mathbb{1}-Z_1Z_2)\,E|\psi\rangle_L|1\rangle_{A_1} \longrightarrow$$
$$\frac{1}{4}(\mathbb{1}-Z_1Z_2)(\mathbb{1}+Z_2Z_3)\,E|\psi\rangle_L|1\rangle_{A_1}|0\rangle_{A_2} + \frac{1}{4}(\mathbb{1}-Z_1Z_2)(\mathbb{1}-Z_2Z_3)\,E|\psi\rangle_L|1\rangle_{A_1}|1\rangle_{A_2}$$

Collecting all four terms gives the result above. $\checkmark$

</div>
</details>

Each term corresponds to one of the four possible two-bit syndromes $S = s_1 s_2 \in \{00, 01, 10, 11\}$ read off from $(A_1, A_2)$.

### Example: X Error on the Middle Qubit

Suppose $E = X_2$. The corrupted state is:

$$X_2|\psi\rangle_L = \alpha|010\rangle + \beta|101\rangle$$

**Syndrome bit $s_1$ from $Z_1 Z_2$:**

$$Z_1 Z_2 (\alpha|010\rangle + \beta|101\rangle) = (+1)(-1)\alpha|010\rangle + (-1)(+1)\beta|101\rangle = -X_2|\psi\rangle_L$$

Eigenvalue $-1$ $\Rightarrow$ $s_1 = 1$.

**Syndrome bit $s_2$ from $Z_2 Z_3$:**

$$Z_2 Z_3 (\alpha|010\rangle + \beta|101\rangle) = (-1)(+1)\alpha|010\rangle + (+1)(-1)\beta|101\rangle = -X_2|\psi\rangle_L$$

Eigenvalue $-1$ $\Rightarrow$ $s_2 = 1$.

The syndrome is $S = 11$. Only the fourth branch survives:

$$\frac{1}{4}(\mathbb{1} - Z_1Z_2)(\mathbb{1} - Z_2Z_3)\,X_2|\psi\rangle_L|1\rangle_{A_1}|1\rangle_{A_2} = X_2|\psi\rangle_L|1\rangle_{A_1}|1\rangle_{A_2}$$

The ancillas are both measured as $1$, uniquely identifying an $X_2$ error.

## Syndrome Table

The complete syndrome table for all single- and two-qubit bit-flip errors on the three-qubit code is given below. Each single-qubit error produces a unique two-bit syndrome, allowing us to choose the correct recovery operation.

| Error | Syndrome $S$ | Error | Syndrome $S$ |
|-------|:-----------:|-------|:-----------:|
| $I_1 I_2 I_3$ | 00 | $X_1 X_2 I_3$ | 01 |
| $X_1 I_2 I_3$ | 10 | $I_1 X_2 X_3$ | 10 |
| $I_1 X_2 I_3$ | 11 | $X_1 I_2 X_3$ | 11 |
| $I_1 I_2 X_3$ | 01 | $X_1 X_2 X_3$ | 00 |

From this table we see that each single-qubit error produces a unique syndrome, enabling unambiguous identification and correction. Note that some two-qubit errors share syndromes with single-qubit errors — the code is designed to correct single-qubit errors only, and two or more simultaneous errors will be misidentified.
