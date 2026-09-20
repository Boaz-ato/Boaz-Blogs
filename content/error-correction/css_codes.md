---
title: "CSS Codes"
weight: 12
math: true
---

In this article, we will look at the CSS code, which is named after their co-discoverers Robert Calderbank, Peter Shor and Andrew Steane. The CSS code is a special type of stabiliser code. But before we dive into it, we first need to understand classical linear codes.


### Classical Linear Codes

A classical linear code is often referred to as [n, k], which means it encodes $k$ bits of information using $n$ bits. The elements of the encoding are called codewords, and there are $2^k$ codewords in total.

For example, consider a simple [3, 1] repetition code. Here, we encode 1 bit using 3 bits. The encoding is:

$$0 \mapsto 000$$
$$1 \mapsto 111$$

We have $k = 1$, so the number of codewords is $2^1 = 2$, which matches our two codewords: 000 and 111.

For a classical linear code, the distance is defined as the smallest Hamming distance between any two different codewords. The Hamming distance between codewords is the number of positions in which they differ. Why is the distance important? The distance tells us the maximum number of errors that the classical linear code can correct.

Every codeword is $d$ distance away from each other. Now, let's say the distance is $3$. If an error occurs on a codeword, the corrupted codeword is now distance 1 from the original codeword and distance 2 from the other codewords. We can still recover from this error since it has the lowest distance from the original codeword. If two errors occur on this codeword, it is now distance 2 from the original codeword and distance 1 from the other codeword. Now, we cannot recover the original codeword since it does not have the smallest distance with the corrupted codeword. As such, for a code with distance $d$, it can only correct $t$ errors; where $t = \lfloor(d - 1)/2\rfloor$.

A code [n, k] can also be referred to as [n, k, d].

There are two matrices associated with a linear code that make encoding and error detection and correction possible: a generator matrix and a parity check matrix.

A generator matrix $G$ for an [n, k] linear code C is a $k \times n$ matrix:

$$C = \{Gx : x \in \mathbb{Z}_2^k \}$$

To encode a string, we simply multiply by $G$.

A parity check matrix $H$ for an [n, k] linear code C is an $(n-k) \times n$ matrix such that:

$$ C = \{y \in \mathbb{Z}_2^n : Hy = 0\}$$

Here, $y$ is a codeword and $Hy$ gives the syndrome. How does the parity check matrix help in error detection and correction? Suppose we have a codeword $y \in C$ and errors occur on one of the bits. We can write the resulting string as $y^{\prime} = y + e$, where $e$ indicates where the errors took place (a 1 in each position where an error takes place, and 0 everywhere else). We see that $Hy^{\prime} = H(y + e) = He$. The syndrome is only dependent on the error. If the Hamming weight of $e$ is small enough, the syndrome $He$ will allow us to uniquely determine the error vector $e$. More specifically, there cannot be two distinct strings $e$ and $e^{\prime}$ both of Hamming weight less than $d/2$ such that $He^{\prime} = He$.


#### Example: The [7, 4] Hamming Code

Let's look at the [7, 4] Hamming code. This code encodes 4 bits of information using 7 bits, and has distance 3.

The generator matrix $G$ is:

$$
G = \begin{pmatrix}
1 & 0 & 0 & 0 & 1 & 1 & 0 \\\\
0 & 1 & 0 & 0 & 1 & 0 & 1 \\\\
0 & 0 & 1 & 0 & 0 & 1 & 1 \\\\
0 & 0 & 0 & 1 & 1 & 1 & 1
\end{pmatrix}
$$

The parity check matrix $H$ is:

$$
H = \begin{pmatrix}
1 & 1 & 0 & 1 & 1 & 0 & 0 \\\\
1 & 0 & 1 & 1 & 0 & 1 & 0 \\\\
0 & 1 & 1 & 1 & 0 & 0 & 1
\end{pmatrix}
$$

Let's encode the message $x = (1, 0, 1, 1)$. We compute $y = xG$:

$$y = (1, 0, 1, 1) \cdot G = (1, 0, 1, 1, 0, 1, 0)$$

We can verify that $Hy = 0$:

$$
H \cdot (1, 0, 1, 1, 0, 1, 0)^T = (0, 0, 0)^T
$$

The syndrome is zero, confirming this is a valid codeword.

Now suppose an error occurs on the third bit. The corrupted codeword becomes $y' = (1, 0, 0, 1, 0, 1, 0)$. The syndrome is:

$$
H \cdot (1, 0, 0, 1, 0, 1, 0)^T = (0, 1, 1)^T
$$

This non-zero syndrome tells us an error has occurred. The syndrome $(0, 1, 1)^T$ corresponds to the third column of $H$, which uniquely identifies that the error occurred on the third bit. We can then flip this bit to recover the original codeword.


#### Dual Codes

We need to address one last concept before moving on to the CSS codes: the dual code. Suppose that we have an [n, k] linear code C. The dual code is denoted $C^{\perp}$, and contains all strings that have a dot product of 0 with all codewords of C:

$$C^{\perp} = \{y \in \mathbb{Z}_2^n : y \cdot x = 0 \text{ for all } x \in C\}$$

$C^{\perp}$ is an [n, n-k] linear code. If we have a generator matrix $G$ and a parity check matrix $H$ for a code C, then the generator matrix for $C^{\perp}$ is $H^T$ and the parity check matrix is $G^T$.



## The CSS Code

We can now introduce the CSS code. In order to define a CSS code, we must have two classical linear codes $C_1$ and $C_2$ that satisfy certain properties:

1. $C_1$ must be an [n, $k_1$] linear code and $C_2$ must be an [n, $k_2$] linear code with $k_2 < k_1$.
2. It must be that $C_2 \subseteq C_1$. That is, every codeword in $C_2$ must also be in $C_1$.
3. If both $C_1$ and $C_2^{\perp}$ can correct up to $t$ errors, then the resulting CSS code will be an $[[n, k_1 - k_2]]$ code that can correct up to $t$ quantum errors.

Given two classical codes $C_1$ and $C_2$, how do you actually write down the quantum codewords? We need to define the logical basis that gives the valid subspace within which the quantum codewords live. To do this we need to introduce the concept of a coset.

A coset of $C_2$ is the set obtained by adding a fixed string $x$ to every element of $C_2$:

$$ x + C_2 = \{x + y : y \in C_2\}$$

We know that $C_2 \subseteq C_1$; as such we can separate the codewords of $C_1$ into disjoint cosets. Note that if $x_i + x_j \in C_2$, the cosets formed by $x_i$ and $x_j$ are the same. However, if $x_i + x_j \notin C_2$, the cosets formed by $x_i$ and $x_j$ are disjoint.

The code $C_1$ has $2^{k_1}$ codewords and the code $C_2$ has $2^{k_2}$ codewords. Hence, the number of cosets is given as:

$$\frac{2^{k_1}}{2^{k_2}} = 2^{k_1 - k_2}$$

Each coset defines a logical basis state. Therefore the dimension of the CSS code is $2^{k_1 - k_2}$ and it encodes $k_1 - k_2$ qubits.

The logical basis of the CSS code is given as:

$$
|j\rangle \mapsto |x_j + C_2\rangle = \frac{1}{\sqrt{|C_2|}} \sum_{y \in C_2} |x_j + y\rangle
$$

Here notice that $\langle x_i + C_2 | x_j + C_2 \rangle = 0$ for $i \neq j$ follows from the fact that $x_i + x_j \notin C_2$ for $i \neq j$.

A general logical state is encoded by linearity:

$$
\sum_j \alpha_j|j \rangle \mapsto \sum_j \alpha_j|x_j + C_2 \rangle
$$


Now we have a way of defining the logical basis of the CSS code. The general steps to detect and fix errors in CSS codes are as follows:

1. Compute $C_1$'s syndrome into ancilla qubits, measure it and apply NOT gates to fix the bit-flipped qubits.
2. Apply a Hadamard to every qubit, which turns phase flips into bit flips.
3. Compute $C_2^{\perp}$'s syndrome into ancilla qubits, measure it, and apply NOT gates to fix the flipped qubits.
4. Apply a Hadamard to every qubit again to return to the original basis.

How does this relate to the stabiliser codes?

CSS codes are a special subclass of stabiliser codes. A stabiliser code is CSS when its stabiliser generators can be split into two groups: some made only of X's and some made only of Z's.

The two groups come straight from the two classical codes. Each row of $C_1$'s parity check matrix gives a Z-type generator. For example, the row 0001111 becomes $I \otimes I \otimes I \otimes Z \otimes Z \otimes Z \otimes Z$. Measuring these detects bit-flips. Each row of $C_2^{\perp}$'s parity check matrix gives an X-type generator, which detects phase-flips.


#### Example: Constructing a CSS Code

Let's construct a CSS code using simple classical codes. We will use the [7, 4] Hamming code as $C_1$ and a [7, 3] code as $C_2$.

**Step 1: Define the classical codes**

Let $C_1$ be the [7, 4] Hamming code with parity check matrix:

$$
H_1 = \begin{pmatrix}
1 & 1 & 0 & 1 & 1 & 0 & 0 \\\\
1 & 0 & 1 & 1 & 0 & 1 & 0 \\\\
0 & 1 & 1 & 1 & 0 & 0 & 1
\end{pmatrix}
$$

Let $C_2$ be the [7, 3] code, which is a subcode of $C_1$. For simplicity, we take $C_2 = C_1^{\perp}$, the dual of the Hamming code. This is a [7, 3] code with generator matrix $H_1$.

We can verify that $C_2 \subseteq C_1$: since $C_2 = C_1^{\perp}$, every codeword in $C_2$ is orthogonal to itself, meaning $C_2 \subseteq C_2^{\perp} = C_1$.

**Step 2: Determine the CSS code parameters**

The CSS code encodes $k_1 - k_2 = 4 - 3 = 1$ qubit. This is the famous [[7, 1, 3]] Steane code.

**Step 3: Construct the logical basis**

We have $2^{k_1 - k_2} = 2^1 = 2$ cosets. The logical basis states are:

$$
|\bar{0}\rangle = \frac{1}{\sqrt{8}} \sum_{y \in C_2} |y\rangle
$$

$$
|\bar{1}\rangle = \frac{1}{\sqrt{8}} \sum_{y \in C_2} |x_1 + y\rangle
$$

where $x_1$ is a codeword in $C_1$ but not in $C_2$. For the Steane code, we can write these explicitly:

$$
|\bar{0}\rangle = \frac{1}{\sqrt{8}} (|0000000\rangle + |1010101\rangle + |0110011\rangle + |1100110\rangle + |0001111\rangle + |1011010\rangle + |0111100\rangle + |1101001\rangle)
$$

$$
|\bar{1}\rangle = \frac{1}{\sqrt{8}} (|1111111\rangle + |0101010\rangle + |1001100\rangle + |0011001\rangle + |1110000\rangle + |0100101\rangle + |1000011\rangle + |0010110\rangle)
$$

**Step 4: Derive the stabiliser generators**

From the parity check matrix $H_1$ of $C_1$, we get the Z-type stabiliser generators:

- Row 1: $1101100 \rightarrow Z \otimes Z \otimes I \otimes Z \otimes Z \otimes I \otimes I$
- Row 2: $1011010 \rightarrow Z \otimes I \otimes Z \otimes Z \otimes I \otimes Z \otimes I$
- Row 3: $0111001 \rightarrow I \otimes Z \otimes Z \otimes Z \otimes I \otimes I \otimes Z$

From the parity check matrix of $C_2^{\perp} = C_1$, we get the X-type stabiliser generators:

- Row 1: $1101100 \rightarrow X \otimes X \otimes I \otimes X \otimes X \otimes I \otimes I$
- Row 2: $1011010 \rightarrow X \otimes I \otimes X \otimes X \otimes I \otimes X \otimes I$
- Row 3: $0111001 \rightarrow I \otimes X \otimes X \otimes X \otimes I \otimes I \otimes X$

We can verify that all these generators commute with each other. The X-type and Z-type generators commute because any X generator and Z generator share an even number of positions where both have non-identity operators (a property guaranteed by $C_2 \subseteq C_1$).

This is the Steane code, one of the most important CSS codes in quantum error correction.

