---
title: "How to Add Redundancy to Quantum Error Correction"
weight: 5
math: true
---

## The Problem with Copying

Classical codes work with the assumption that we can simply copy the information we want to protect. However, the no-cloning theorem in quantum mechanics asserts that it is not possible to construct an operator that performs the following operation:

$$|\psi\rangle|0\rangle \rightarrow |\psi\rangle|\psi\rangle$$

where $|\psi\rangle$ is the state to be cloned. As such, quantum error correction needs to find an alternative approach to adding redundancy to the system, which will allow errors to be detected.

## Redundancy Through Entanglement

In quantum information, redundancy is achieved by expanding the Hilbert space. We entangle the qubit we want to protect with additional qubits, spreading the logical information across a larger system.

To make this concrete, let's consider adding redundancy to a single qubit using three physical qubits. The circuit used to add redundancy is given below.

![Circuit used to add redundancy to a qubit](/images/encoding.png)

The circuit uses two CNOT gates, with the original qubit as the control and the two extra qubits, initialised to $|0\rangle$, as targets. It takes the state $|\psi\rangle = \alpha|0\rangle + \beta|1\rangle$ and produces:

$$|\psi\rangle = \alpha |0\rangle + \beta |1\rangle \xrightarrow{\text{encode}} |\psi\rangle_L = \alpha |000\rangle + \beta |111\rangle$$

where $|000\rangle$ and $|111\rangle$ are the **logical codewords**. This is not a cloned state. Cloning would give:

$$|\psi\rangle \otimes |\psi\rangle \otimes |\psi\rangle = \bigl(\alpha|0\rangle + \beta|1\rangle\bigr)^{\otimes 3}$$

which expands into eight terms with all combinations of $\alpha$ and $\beta$. The encoded state $\alpha|000\rangle + \beta|111\rangle$ has only two terms — the logical information is spread across the qubits through entanglement, not copying.

## The Codespace

The encoding operation gives us a geometric picture of how redundancy enables error detection. Before encoding, the single qubit lives in a two-dimensional Hilbert space:

$$|\psi\rangle \in \mathcal{H}_2 = \text{span}\{|0\rangle,\, |1\rangle\}$$

After encoding with three physical qubits, the system lives in an eight-dimensional Hilbert space $\mathcal{H}_8$. But the logical qubit does not occupy all of it. It is confined to a two-dimensional subspace:

$$|\psi\rangle_L \in \mathcal{C} = \text{span}\{|000\rangle,\, |111\rangle\} \subset \mathcal{H}_8$$

This subspace $\mathcal{C}$ is called the **codespace** — the corner of the full Hilbert space where valid, uncorrupted logical states live.

## Errors Move the State Out of the Codespace

Now suppose a bit-flip error occurs on the first qubit. The corrupted state becomes:

$$X_1|\psi\rangle_L = \alpha|100\rangle + \beta|011\rangle$$

This state is no longer in the codespace $\mathcal{C}$. It has been rotated into a different two-dimensional subspace:

$$X_1|\psi\rangle_L \in \mathcal{F} = \text{span}\{|100\rangle,\, |011\rangle\} \subset \mathcal{H}_8$$

We call $\mathcal{F}$ the **error subspace**. Crucially, $\mathcal{C}$ and $\mathcal{F}$ are mutually orthogonal — they share no overlap. An uncorrupted logical state lives in $\mathcal{C}$; a state that has suffered a bit-flip on the first qubit lives in $\mathcal{F}$. Because the two subspaces are orthogonal, we can perform a projective measurement that determines which subspace the state occupies, without learning anything about the values of $\alpha$ and $\beta$.

Think of the quantum state as a vector living in an eight-dimensional space, spanned by the eight computational basis states $\{|000\rangle, |001\rangle, |010\rangle, |011\rangle, |100\rangle, |101\rangle, |110\rangle, |111\rangle\}$. When no error has occurred, the state lives entirely within the codespace, with components only along the $|000\rangle$ and $|111\rangle$ axes. When an error occurs, the state is rotated out of the codespace, and which error subspace it lands in tells us exactly what error occurred.

The key insight is that this measurement never reveals the values of $\alpha$ and $\beta$. It only tells us which error subspace the state has been rotated into. The actual quantum information remains untouched throughout — we can detect and identify the error without ever disturbing the logical information itself.
