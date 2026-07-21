---
title: "Stabilizer Measurements:  Detecting Phase Errors"
weight: 7
math: true
---

<style>
.bb-derive {
  border-left: 3px solid #ea580c;
  border-radius: 0 6px 6px 0;
  margin: 1.2rem 0;
  background: rgba(234,88,12,0.04);
}
.bb-derive summary {
  padding: 0.6rem 1rem;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.9rem;
  color: #ea580c;
  user-select: none;
}
.bb-derive summary:hover { background: rgba(234,88,12,0.08); border-radius: 0 6px 6px 0; }
.bb-derive .derive-body { padding: 0.5rem 1.2rem 1rem; }
.bb-derive .derive-body p { margin: 0.55rem 0; font-size: 0.92rem; line-height: 1.7; }
</style>

So far, we know that any single-qubit error can be expressed in terms of the X and Z Pauli gates. We have also seen how to detect and correct bit-flip errors using Z-type stabiliser measurements. The obvious question is: can we use Z-type stabilisers to detect phase errors?

## Why Z-type Stabilisers Fail for Phase Errors

Let's again look at the encoding using two physical qubits:

$$|\psi\rangle = \alpha |0\rangle + \beta |1\rangle \xrightarrow{\text{encode}} |\psi\rangle_L = \alpha |00\rangle + \beta |11\rangle$$

For the Z-type stabilisers to detect phase errors, the corrupted state must be the $-1$ eigenstate of the stabiliser operator. Let's act the $Z_1Z_2$ operator on the logical state with a phase error on the first qubit:

$$Z_1Z_2 \left(Z_1|\psi\rangle_L\right) = Z_1Z_2\left(\alpha |00\rangle - \beta |11\rangle\right) = \alpha |00\rangle - \beta |11\rangle$$

The corrupted state $Z_1|\psi\rangle_L$ is the **+1** eigenstate of $Z_1Z_2$, not the $-1$ eigenstate. Therefore, Z-type stabilisers cannot distinguish a phase-flipped state from a healthy one — they are blind to phase errors.

## The Hadamard Trick: Rotating the Basis

We need a different approach. To understand how to detect phase errors, we first recall how the Hadamard gate acts on the computational basis:

$$H|0\rangle = \frac{|0\rangle + |1\rangle}{\sqrt{2}} = |+\rangle$$

$$H|1\rangle = \frac{|0\rangle - |1\rangle}{\sqrt{2}} = |-\rangle$$

The Hadamard gate rotates the basis from $\{|0\rangle, |1\rangle\}$ to $\{|+\rangle, |-\rangle\}$. To see why this is useful, recall how the X and Z gates act in the computational basis:

$$X\left(\alpha|0\rangle + \beta|1\rangle\right) = \alpha|1\rangle + \beta|0\rangle$$
$$Z\left(\alpha|0\rangle + \beta|1\rangle\right) = \alpha|0\rangle - \beta|1\rangle$$

Now consider how these same gates act in the $\{|+\rangle, |-\rangle\}$ basis:

$$X|+\rangle = |+\rangle, \qquad X|-\rangle = -|-\rangle \quad \text{(acts like a phase flip)}$$
$$Z|+\rangle = |-\rangle, \qquad Z|-\rangle = |+\rangle \quad \text{(acts like a bit flip)}$$

In the $\{|+\rangle, |-\rangle\}$ basis, **X acts as a phase flip and Z acts as a bit flip** — the roles of the two error types are exactly swapped. The Hadamard transform exchanges bit-flip and phase-flip errors.

This means that if we encode the logical qubit in the $\{|+\rangle, |-\rangle\}$ basis, a phase error (Z gate) becomes a bit-flip in that basis, and we can then detect it using X-type stabilisers — the Hadamard-conjugate counterpart of the Z-type stabilisers.

## The Encoding Circuit for Detecting Phase-Flip Errors

Encoding the logical qubit into the $\{|+\rangle, |-\rangle\}$ basis gives:

$$|\psi\rangle = \alpha |0\rangle + \beta |1\rangle \xrightarrow{\text{encode}} |\psi\rangle_L = \alpha |{+}{+}{+}\rangle + \beta |{-}{-}{-}\rangle$$

![Encoding circuit for detecting phase errors](/images/encoding_circuit_phase.webp)

This is the same encoding circuit used for detecting bit-flips, with Hadamard gates applied to all data qubits at the end.

## X-type Stabilisers Detect Phase Errors

Since Z acts as a bit-flip in the $\{|+\rangle, |-\rangle\}$ basis, X-type stabilisers can detect phase errors in the same way that Z-type stabilisers detect bit-flip errors. Let's verify this for the operator $X_1X_2$.

First, confirm the logical state is a $+1$ eigenstate:

$$X_1X_2 \left(|\psi\rangle_L\right) = X_1X_2\left(\alpha |{+}{+}{+}\rangle + \beta |{-}{-}{-}\rangle\right) = \alpha |{+}{+}{+}\rangle + \beta |{-}{-}{-}\rangle$$

The $X_1X_2$ operator stabilises the logical state. Now, suppose a phase error occurs on the first qubit:

$$X_1X_2 \left(Z_1|\psi\rangle_L\right) = X_1X_2\left(\alpha |{-}{+}{+}\rangle + \beta |{+}{-}{-}\rangle\right) = (-1)\left(\alpha |{-}{+}{+}\rangle + \beta |{+}{-}{-}\rangle\right)$$

The corrupted state is a $-1$ eigenstate of $X_1X_2$. We can therefore use X-type stabilisers to detect phase errors.

## Putting It All Together

We encode the logical qubit using three physical qubits. A phase error from the set $\{Z_1, Z_2, Z_3\}$ may then occur on one of them. We apply syndrome measurements using the two X-type stabilisers $X_1X_2$ and $X_2X_3$; the measurement outcomes identify which qubit was affected.

![Circuit showing the encoding, error, and syndrome measurement stages](/images/phase_error_detection.png)

The syndrome extraction circuit acts on the encoded (and possibly corrupted) state $E|\psi\rangle_L$ together with two ancilla qubits initialised to $|00\rangle_A$. The full output is a superposition over all four syndrome branches:

$$E|\psi\rangle_L |00\rangle_A \xrightarrow{\text{syndrome extraction}} \frac{1}{4}\Bigl[(\mathbb{1} + X_1X_2)(\mathbb{1} + X_2X_3)\,E|\psi\rangle_L\,|00\rangle_A$$
$$+\;(\mathbb{1} - X_1X_2)(\mathbb{1} + X_2X_3)\,E|\psi\rangle_L\,|10\rangle_A$$
$$+\;(\mathbb{1} - X_1X_2)(\mathbb{1} - X_2X_3)\,E|\psi\rangle_L\,|11\rangle_A$$
$$+\;(\mathbb{1} + X_1X_2)(\mathbb{1} - X_2X_3)\,E|\psi\rangle_L\,|01\rangle_A\Bigr]$$

Measuring the ancillas collapses the state into exactly one branch. Since $X_1X_2$ and $X_2X_3$ are the phase-flip stabilisers, any $Z_i$ error anticommutes with exactly the stabilisers that act on qubit $i$, yielding a unique $-1$ outcome for each possible error location.

<details class="bb-derive">
<summary>Step-by-step: syndrome extraction on the encoded state</summary>
<div class="derive-body">

The encoded state after a possible phase error on qubit $i$ is acted on by each stabiliser in turn. The ancilla qubit for each stabiliser is initialised to $|0\rangle$ and entangled with the data qubits via controlled-X gates, then measured in the Z basis.

**No error — $M_0, M_1 = 0, 0$**

$$X_1X_2\left(\alpha|{+}{+}{+}\rangle + \beta|{-}{-}{-}\rangle\right) = +1 \cdot \left(\alpha|{+}{+}{+}\rangle + \beta|{-}{-}{-}\rangle\right)$$
$$X_2X_3\left(\alpha|{+}{+}{+}\rangle + \beta|{-}{-}{-}\rangle\right) = +1 \cdot \left(\alpha|{+}{+}{+}\rangle + \beta|{-}{-}{-}\rangle\right)$$

Both ancillas measure 0.

**Error $Z_1$ — $M_0, M_1 = 1, 0$**

The corrupted state is $\alpha|{-}{+}{+}\rangle + \beta|{+}{-}{-}\rangle$.

$$X_1X_2\left(\alpha|{-}{+}{+}\rangle + \beta|{+}{-}{-}\rangle\right) = -1 \cdot \left(\alpha|{-}{+}{+}\rangle + \beta|{+}{-}{-}\rangle\right)$$
$$X_2X_3\left(\alpha|{-}{+}{+}\rangle + \beta|{+}{-}{-}\rangle\right) = +1 \cdot \left(\alpha|{-}{+}{+}\rangle + \beta|{+}{-}{-}\rangle\right)$$

First ancilla measures 1, second measures 0.

**Error $Z_2$ — $M_0, M_1 = 1, 1$**

The corrupted state is $\alpha|{+}{-}{+}\rangle + \beta|{-}{+}{-}\rangle$.

$$X_1X_2\left(\alpha|{+}{-}{+}\rangle + \beta|{-}{+}{-}\rangle\right) = -1 \cdot \left(\alpha|{+}{-}{+}\rangle + \beta|{-}{+}{-}\rangle\right)$$
$$X_2X_3\left(\alpha|{+}{-}{+}\rangle + \beta|{-}{+}{-}\rangle\right) = -1 \cdot \left(\alpha|{+}{-}{+}\rangle + \beta|{-}{+}{-}\rangle\right)$$

Both ancillas measure 1.

**Error $Z_3$ — $M_0, M_1 = 0, 1$**

The corrupted state is $\alpha|{+}{+}{-}\rangle + \beta|{-}{-}{+}\rangle$.

$$X_1X_2\left(\alpha|{+}{+}{-}\rangle + \beta|{-}{-}{+}\rangle\right) = +1 \cdot \left(\alpha|{+}{+}{-}\rangle + \beta|{-}{-}{+}\rangle\right)$$
$$X_2X_3\left(\alpha|{+}{+}{-}\rangle + \beta|{-}{-}{+}\rangle\right) = -1 \cdot \left(\alpha|{+}{+}{-}\rangle + \beta|{-}{-}{+}\rangle\right)$$

First ancilla measures 0, second measures 1.

</div>
</details>

The syndrome measurement on the two ancilla qubits maps to the following error table:

| Syndrome $(M_0 M_1)$ | Error | Qubit |
|:---:|:---:|:---:|
| 00 | No error | — |
| 10 | Phase flip $Z$ | $q_1$ |
| 11 | Phase flip $Z$ | $q_2$ |
| 01 | Phase flip $Z$ | $q_3$ |

Each syndrome uniquely identifies which qubit suffered a phase flip. Applying the corresponding $Z$ gate to that qubit restores the logical state.
