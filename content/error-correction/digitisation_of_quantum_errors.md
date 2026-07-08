---
title: "Digitisation of Quantum Errors"
weight: 4
math: true
---

As established in the previous section, the state of a qubit can be expressed as:

$$|\psi\rangle = \cos\frac{\theta}{2}|0\rangle + e^{i\varphi}\sin\frac{\theta}{2}|1\rangle$$

which represents a point on the surface of the Bloch sphere. An error on a qubit can be pictured as a rotation that moves the state from one point on the sphere to another. This type of error is known as a **coherent error**, and can arise from systematic control faults in the hardware used to realise the qubits.

Mathematically, coherent errors are described by a unitary operator that evolves the state as follows:

$$U(\delta\theta, \delta\varphi)|\psi\rangle = \cos\frac{\theta + \delta\theta}{2}|0\rangle + e^{i(\varphi + \delta\varphi)}\sin\frac{\theta + \delta\theta}{2}|1\rangle$$

where $\theta + \delta\theta$ and $\varphi + \delta\varphi$ are the new coordinates on the Bloch sphere. From this, we see that qubits are susceptible to a continuum of coherent errors, obtained by varying the parameters $\delta\theta$ and $\delta\varphi$.

It would therefore seem that quantum error correction protocols would need to be based on techniques from classical analogue computation, for which the theory of error correction is not well developed. Fortunately, it turns out that quantum errors can be **digitised**, so that the ability to correct a finite set of errors is sufficient to correct any error.

---

## The Pauli Basis

This digitisation is made possible by the fact that any coherent error can be expressed in terms of the **Pauli operators**. The four single-qubit Pauli operators are:

$$I = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix}, \quad X = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix}, \quad Y = \begin{pmatrix} 0 & -i \\ i & 0 \end{pmatrix}, \quad Z = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix}$$

These four matrices form a basis for all $2 \times 2$ complex matrices. Their physical meaning is:

- $I$ — the identity; no operation applied
- $X$ — a bit flip: $|0\rangle \leftrightarrow |1\rangle$ (the quantum analogue of a classical bit flip)
- $Z$ — a phase flip: $|0\rangle \to |0\rangle$, $|1\rangle \to -|1\rangle$ (no classical analogue)
- $Y$ — a combined bit and phase flip; equivalent to $XZ$ up to a global phase

## Decomposing Errors into the Pauli Basis

Any single-qubit coherent error can be decomposed in terms of the Pauli operators as follows:

$$U(\delta\theta, \delta\varphi)|\psi\rangle = \alpha_I\, I|\psi\rangle + \alpha_X\, X|\psi\rangle + \alpha_Z\, Z|\psi\rangle + \alpha_Y\, Y|\psi\rangle$$

where $\alpha_I$, $\alpha_X$, $\alpha_Z$, and $\alpha_Y$ are complex expansion coefficients. Since the Pauli $Y$ matrix is equivalent (up to a global phase) to the product $XZ$, we can further simplify this as:

$$U(\delta\theta, \delta\varphi)|\psi\rangle = \alpha_I\, I|\psi\rangle + \alpha_X\, X|\psi\rangle + \alpha_Z\, Z|\psi\rangle + \alpha_{XZ}\, XZ|\psi\rangle$$

This expression shows that any coherent error process can be decomposed into a superposition from the Pauli set $\{I, X, Z, XZ\}$. Quantum error correction involves performing projective measurements that cause this superposition to collapse to a single one of its terms. As a result, being able to detect and correct $X$ and $Z$ Pauli errors is sufficient to correct any coherent error — no matter how complicated, the error is always a superposition of just four possibilities: no error, a bit flip, a phase flip, or both simultaneously.

This effect — the **digitisation of errors** — is the central insight of quantum error correcting codes.

---

## What the Hardware Must Guarantee

Digitisation simplifies the software side of error correction enormously, but it does not eliminate hardware requirements. For this error model to work, the physical device must satisfy two conditions:

1. **Two-level systems** — each qubit must be a true two-level system: the computational basis must be exactly $|0\rangle$ and $|1\rangle$, with no leakage into higher energy levels.
2. **Localised noise** — noise must occur at specific, identifiable moments in the circuit: after single-qubit gates, after two-qubit gates, before measurements, and after initialisation.

![Conditions on the hardware](/images/error.png)

If these conditions hold, then detecting and correcting bit flips and phase flips in software is sufficient to handle any physical error the hardware produces.
