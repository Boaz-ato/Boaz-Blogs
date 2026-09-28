---
title: "Classical Error Correction"
weight: 2
math: true
---

In classical information processing, data is represented as a sequence of bits taking values `0` or `1`. There is only one possibility for an error to occur: either a `0` bit changes into a `1` or vice versa. Such errors can arise from various sources, including electromagnetic interference, thermal noise, voltage fluctuations, or physical defects in storage media. To protect against errors, we need to add redundancy such that when an error occurs, we can still determine the correct information stored in the bits. Error correction is a protocol that recovers the correct information stored in bits in the presence of noise. The basic idea behind error correction is to increase the number of bits used to encode a given amount of information.

## The Repetition Code

Let's consider a type of error correction called the repetition code. In this protocol, each bit is encoded using multiple bits. For example:

- `0` → `000`
- `1` → `111`

Here, `000` and `111` are referred to as the **logical codewords**.

Using this encoding, suppose we want to store the bit `0`. We first encode it as `000`. Now suppose the middle bit experiences an error and flips from `0` to `1` — the encoded state becomes `010`. We can apply a **majority vote** to recover the intended message: two of the three bits are `0`, so we correctly decode to `0`. The same logic applies if a single bit flip occurs on any of the three positions.

However, if more than one bit flip occurs, the majority vote fails. For example, if the first and second bits both flip, `000` becomes `110`, which majority-decodes to `1` — an incorrect result. There is also the case where all three bits flip, changing `000` to `111`. Since `111` is itself a valid codeword, we have no way of knowing an error has occurred.

## Code Distance

The **distance** of an error correction code is the minimum number of bit flips needed to transform one valid codeword into another. Let's consider the two codewords `000` and `111`. They differ in three positions, so the distance between them is `3`. Now, suppose a single error occurs on the codeword `000`, and it becomes `010`. The corrupted bit string is one position away from `000` and two positions away from `111`. Let's say another error occurs on the corrupted bitstring and it becomes `011`. It is now two positions away from the original codeword `000` and one position away from `111`. With two errors, it is closer to the wrong codeword than to its original encoding, and it is now impossible to correctly recover the original codeword. 


As such, we can relate the distance $d$ to the number of errors $t$ the code can correct:

$$d = 2t + 1$$

For the three-bit repetition code, $d = 3$, so it can correct $t = 1$ error.

## All Possible Outcomes

With three bits and independent errors, there are eight possible states after passing through a noise channel:

| Received | Number of errors | Majority vote decodes to | Outcome |
|----------|-----------------|--------------------------|---------|
| `000`    | 0               | `0`                      | Correct |
| `001`    | 1               | `0`                      | Correct |
| `010`    | 1               | `0`                      | Correct |
| `100`    | 1               | `0`                      | Correct |
| `011`    | 2               | `1`                      | **Error** |
| `101`    | 2               | `1`                      | **Error** |
| `110`    | 2               | `1`                      | **Error** |
| `111`    | 3               | `1`                      | **Error** |

The majority vote succeeds whenever at most one bit flips, and fails when two or more bits flip.

## Logical Error Rate

As we have seen, the three-bit repetition code fails when two or more errors occur. The probability that two or more errors occur out of three independent bits, each with error rate $p$, is:

$$P_{\text{fail}} = 3p^2 - 2p^3$$

For the three-bit repetition code to be worth it, the probability of failure using the code, $P_{\text{fail}}$, must be less than the probability of an error with no encoding. The repetition code is an improvement whenever $P_{\text{fail}} < p$, which holds for any $p < 0.5$.

The plot below shows the logical error rate (probability of failure) of the three-bit repetition code versus the physical error rate $p$. As seen, the three-bit repetition code begins to help when the probability of an error on the bits is less than $0.5$. 

![Logical error rate of the three-bit repetition code](/images/classical_error_rate.png)

## Code Notation

Error correction codes are described using the **[n, k, d]** notation, where:

- $n$ — total number of bits per codeword
- $k$ — number of encoded bits
- $d$ — code distance

Under this notation, the three-bit repetition code is labelled **[3, 1, 3]**: it uses 3 physical bits to encode 1 logical bit with distance 3.
