---
title: "Classical Error Correction"
weight: 2
---

## Bits, Noise, and Redundancy

Before quantum systems, engineers faced a simpler but still non-trivial problem:
how do you reliably send a message over a noisy channel?

The mathematical model is the **binary symmetric channel (BSC)**: each bit is
flipped independently with some probability `p`. If `p = 0.01`, then 1 in every
100 bits is corrupted. For a 1 MB file (8 million bits), that means ~80,000
errors — clearly unacceptable.

## The Repetition Code

The simplest idea: send each bit three times.

- To send `0`, transmit `000`
- To send `1`, transmit `111`

If one bit flips and you receive `110`, **majority vote** recovers `1`.

This is the **[3,1]-repetition code**: 3 physical bits encode 1 logical bit.
It corrects any single-bit error.

The trade-off: we use 3× the bandwidth/storage to get error correction.

## Hamming Codes

Richard Hamming invented a far more efficient approach in 1950, motivated by
the frustration of his card-reader programs crashing overnight without telling
him where the error was.

The **[7,4] Hamming code** encodes **4 data bits** into **7 bits** using only
3 parity check bits. It corrects any single-bit error.

### How It Works

Bits are placed at positions 1–7. Parity bits occupy positions that are powers
of 2: positions **1, 2, 4**. Each parity bit checks a different subset:

| Parity bit | Checks positions |
|-----------|----------------|
| p₁ (pos 1) | 1, 3, 5, 7 |
| p₂ (pos 2) | 2, 3, 6, 7 |
| p₃ (pos 4) | 4, 5, 6, 7 |

When an error occurs, the pattern of failed parity checks — called the
**syndrome** — directly encodes the *position* of the error bit in binary.
Flip that bit and the message is recovered.

### Efficiency

The repetition code has rate 1/3 (1 useful bit per 3 transmitted). The [7,4]
Hamming code has rate **4/7 ≈ 57%** — much more efficient, while still
correcting one error.

## Shannon's Noisy-Channel Theorem

In 1948, Claude Shannon proved a theorem that changed information theory forever.

For any noisy channel, there is a theoretical maximum rate — the **channel
capacity** C — at which information can be transmitted with **arbitrarily low
error probability**. If you transmit below rate C, perfect communication is
possible. Above rate C, it's not.

For a BSC with error probability `p`:

```
C = 1 - H(p)   where H(p) = -p log₂p - (1-p) log₂(1-p)
```

Shannon's theorem guarantees codes exist that approach this limit — but
it doesn't tell you how to find them. Finding efficient codes that approach
channel capacity was the central problem of coding theory for the next
60 years, culminating in **turbo codes** (1993) and **LDPC codes** (rediscovered
in the 1990s), which come within a fraction of a dB of the Shannon limit.

## Summary

Classical error correction established:

- Redundancy enables reliable communication despite noise
- The syndrome efficiently identifies error locations
- There is a fundamental information-theoretic limit (channel capacity)
- Achieving that limit requires clever, structured codes

Next, we'll see why these classical ideas cannot simply be copied into the
quantum world — and what new concepts are needed to replace them.
