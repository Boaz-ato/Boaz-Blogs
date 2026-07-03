---
title: "Quantum Error Correcting Codes"
weight: 4
---

## The Shor Code

Peter Shor's 1995 nine-qubit code was the proof-of-concept that made the field
of quantum error correction real. It encodes **1 logical qubit** into **9
physical qubits** and corrects *any* single-qubit error — bit flips, phase
flips, or any combination.

### Two-Layer Architecture

The Shor code works by layering two simpler codes:

**Outer layer — phase flip protection:**

The logical qubit `α|0⟩ + β|1⟩` is first encoded as:

```
α · |+⟩|+⟩|+⟩  +  β · |−⟩|−⟩|−⟩
```

where `|+⟩ = (|0⟩+|1⟩)/√2` and `|−⟩ = (|0⟩−|1⟩)/√2`. This 3-qubit code
detects phase (Z) errors via majority vote in the Hadamard basis.

**Inner layer — bit flip protection:**

Each of the three `|+⟩` / `|−⟩` qubits is further encoded as a 3-qubit
bit-flip code:

```
|+⟩  →  (|000⟩ + |111⟩)/√2
|−⟩  →  (|000⟩ − |111⟩)/√2
```

Nine physical qubits total, protecting against both X and Z errors on any
single qubit.

## The Stabilizer Formalism

The Shor code is clever but ad hoc. Daniel Gottesman's **stabilizer formalism**
(1997) gave quantum error correction a unified mathematical framework.

### What Is a Stabilizer Code?

A **stabilizer code** is defined by a set of commuting multi-qubit Pauli
operators `{g₁, g₂, ..., gₙ₋ₖ}` called **stabilizers**. The codewords are
the states simultaneously stabilized (i.e., in the +1 eigenspace) of all
generators.

A code on `n` physical qubits with `n−k` independent stabilizer generators
encodes **k logical qubits**.

### Error Detection via Syndromes

When an error `E` (a Pauli operator) occurs on a codeword `|ψ⟩`:

- If `E` **commutes** with stabilizer `gᵢ`: measuring `gᵢ` gives `+1` (no signal)
- If `E` **anti-commutes** with stabilizer `gᵢ`: measuring `gᵢ` gives `−1` (error flagged)

The pattern of `±1` outcomes across all stabilizers is the **syndrome**. It
uniquely identifies which Pauli error occurred (for correctable codes) without
revealing any logical information.

This is why ancilla measurement works: stabilizer measurement is a joint
property of many qubits, not a measurement of the logical qubit itself.

## The Surface Code

The **surface code** (Kitaev 1997, Fowler et al. 2012) is currently the
leading candidate for fault-tolerant quantum computing.

### Architecture

Qubits are arranged on a 2D grid. Stabilizers are local: each one involves
only qubits on adjacent lattice sites. For a distance-`d` surface code:

- **d² data qubits** (roughly)
- **(d²-1) stabilizers** — alternating X-type and Z-type plaquettes
- Encodes **1 logical qubit**
- Corrects up to **(d-1)/2** errors

### Why the Surface Code Wins

| Feature | Why it matters |
|---------|---------------|
| Local stabilizers | Physically implementable on 2D chip architectures |
| High error threshold (~1%) | Tolerates realistic hardware noise rates |
| Efficient decoders | Minimum-weight perfect matching (MWPM) decoder works well |
| Scalable | Distance can be increased by adding more physical qubits |

Current leading quantum hardware (Google, IBM, Microsoft) targets the surface
code as the route to fault-tolerant quantum computation.

### Code Comparison

| Code | Physical qubits | Logical qubits | Distance | Threshold |
|------|----------------|----------------|----------|-----------|
| [3,1] repetition | 3 | 1 | 1 | — |
| Shor code | 9 | 1 | 3 | — |
| Steane [[7,1,3]] | 7 | 1 | 3 | ~10⁻⁵ |
| Surface code (d=5) | ~25 | 1 | 5 | ~1% |

The surface code's ~1% threshold is transformative: many current quantum
hardware platforms are already operating below this noise level, making
fault-tolerant quantum computation a matter of engineering rather than
fundamental physics.
