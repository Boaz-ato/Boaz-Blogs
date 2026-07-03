---
title: "Introduction"
weight: 1
---

## What Is Error Correction?

Information gets corrupted. Whether a bit flips during storage on a hard drive,
a packet drops in transit over a network, or cosmic rays interfere with a
satellite's memory — errors are a fundamental fact of communication and
computation.

Error correction is the field that asks: *can we detect and fix these errors
automatically, without asking the sender to retransmit?* The answer, remarkably,
is yes — and the theory behind it is one of the most beautiful in all of
computer science and physics.

## The Core Idea

The fundamental strategy is **redundancy**. Instead of sending one copy of
information, we send multiple copies — or more cleverly, we add extra
"check" information that lets the receiver detect and locate any errors that
occurred.

This might sound wasteful, but error correction theory tells us precisely how
much redundancy is needed: often surprisingly little.

## Why It Matters

Error correction is everywhere:

- **Storage** — RAID arrays, SSD error correction, DNA data storage
- **Communication** — Wi-Fi, LTE, satellite links, deep-space probes
- **Computation** — ECC RAM, quantum computers
- **Streaming** — QR codes, Blu-ray discs, streaming video

NASA's Voyager probes, now in interstellar space, would have returned
unreadable images without error correction codes protecting every bit of
the signal across billions of kilometers.

## What You'll Learn

This series covers:

1. **Classical Error Correction** — how repetition codes and Hamming codes protect classical bits
2. **Quantum Basics** — the quantum mechanics obstacles that seemed to rule out quantum error correction
3. **Quantum Codes** — how the Shor code and stabilizer formalism overcome every obstacle
4. **Conclusion** — the bigger picture, open problems, and where to go next

By the end, you'll understand why quantum error correction is considered one of
the most surprising and important discoveries in the history of computing.
