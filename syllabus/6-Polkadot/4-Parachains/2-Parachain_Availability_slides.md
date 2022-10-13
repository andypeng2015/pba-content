---
title: Parachain Availability
description: Polkadot Parachains for Web3 engineers
duration: 1 hour
instructors: ["Bernhard Schuster, Robert Klotzner"]
teaching-assistants: ["Dan Shields"]
---

# Parachain Availability

---

## Parachain Availability

<image src="../../../assets/img/5-Polkadot/bs_impl_arch.svg" style="width: 1400px" >

---

## Path of a Parachain Block

<widget-text center>

- Backing <!-- .element: class="fragment" -->
- Availability <!-- .element: class="fragment" -->
- Approval <!-- .element: class="fragment" -->
- (Disputes) <!-- .element: class="fragment" -->

---

## Path of a Parachain Block

<image src="../../../assets/img/5-Polkadot/bs_block_prod.svg" style="width: 800px">

---

## Polkadot.js

<image src="../../../assets/img/5-Polkadot/bs_polkadot_js_bitfields.png" style="height: 600px">

Part of the `InherentData.enter`

---

## Polkadot.js

<image src="../../../assets/img/5-Polkadot/bs_polkadot_js_inclusion.png" style="width: 600px">

---

## Availability Revisited

<widget-text center>

- What does availability mean?
- Why do we need availability?
- Why not to just distribute the PoV?
- How large is a PoV?
  - Network Bandwidth
  - Storage on chain

Notes:

- Allow retrieval of the PoV from the block producer
- Availability is when we can reconstruct the PoV
- Availability is when we (a validator) have retrieved the chunk for our index, by extension we can then query a sufficient number of validators to recover the original data
- Every validator queries the respective validator index for their chunk, given we know by means of bitfield distribution they have their chunk

---

## Bitfields

---

## Bitfields

<image src="../../../assets/img/5-Polkadot/bs-bitfield-chunk-req.svg" style="width: 700px">

---

## Bitfields

<image src="../../../assets/img/5-Polkadot/bs-bitfield-gossip.svg" style="width: 700px">

---

## Bitfields

- **Compressed representation** - Gossiped availability bitfields, the candidates that are available from the point of view of _one validator_ in the backing group
- The number of bits equivalent to the number of `AvailabilityCore`s
- Submitted on-chain

---

## Bitfields

<image src="../../../assets/img/5-Polkadot/5.4-availability-gossip.svg" style="width: 1000px">

---

## Availability Core

<widget-text center>

- Concept of parachain/thread processing units
- speculative scheduling

---

## Block Availability

<widget-text center>

- `CandidatePendingAvailability.availability_votes`
- Each bit determines if a validator voted for a particular candidate's availability record to be present
- The number of bits is equivalent to the number of `Validator`s in the backing group

---

## Block Availability

<image src="../../../assets/img/5-Polkadot/5.4-relay-block-construction-I.svg" style="width: 1000px">

---

## Block Availability

<image src="../../../assets/img/5-Polkadot/5.4-relay-block-construction-II.svg" style="width: 1000px">

---

## Erasure Coding

Goals:

- Allow to reconstruct the original data from `k` out of `n` chunks, losing `t` chunks at most.

---

## Proceedings

<image src="../../../assets/img/5-Polkadot/bs-erasure-encoding-abstract.svg" style="width: 1000px">

---

## Proceedings

<widget-text center>

- Galois (Extension) Field (static, shared) $\mathbb{F}/p$
- Generator Polynomial (static, shared)

---

## Proceedings

<!-- prettier-ignore -->
\begin{align}
p_x(a) &= \sum^k_{i=1} x_i a^{i-1}\\\\
C(x) &= (p_x(a_1),.. p_x(a_n))\\\\
C(x) &: \mathbb{F}^k \rightarrow \mathbb{F}^n\\\\
\end{align}

---

## Proceedings

$A = \begin{bmatrix}1 & \cdots & 1 & \cdots & 1\\\\ a_1 & \cdots & a_k & \cdots & a_n\\\\ a_1^2 & \cdots & a_k^2 & \cdots & a_n^2\\\\ \vdots & & \vdots & & \vdots\\\\ a_1^{k-1} & \cdots & a_k^{k-1} & \cdots & a_n^{k-1}\end{bmatrix}$

Notes:

- $C(x)$ is a linear mapping
- linear code
- $A$ is the generator matrix
- non-systematic
- DFFT if $a_i$ = $\alpha_i$

---

## Decoding

Matrix inversion

Notes:

- DFFT (only if $\alpha$ is a primitive root of $\mathbb{F}/p$ where $p$ prime and no errors in the chunks received)

---

## Malicious Actors

- Altered chunk data?
- Would cause a PoV / PVF run to fail!

Solution: Supply a merkle proof with each chunk <!-- .element: class="fragment" -->

Notes:

- Use syndromes, more complexity in the decoder, limited error correction and ability

---

## Erasure Coding - Reality

Matrix inversion / multiplication too slow $O(n^3)$

---

## Erasure Coding - Reality

<widget-text center>

- $\mathbb{F}_2^{16}$ (an extension field without primitive roots)
- `u16` as extension field element representation
- no more multiplicative FFT possible

Notes:

- binary representation allows for fast field operations
- not a multiplicative ring anymore

---

## Erasure Coding - Reality

<image src="../../../assets/img/5-Polkadot/bs-erasure-encoding-polkadot.svg" style="width: 1200px">

Notes:

- binary representation allows for fast field operations
- not a multiplicative ring anymore

---

## Polkadot Implementation

Novel Polynomial Basis (paper by Sian-Jheng Lin et. al.):

<https://github.com/paritytech/reed-solomon-novelpoly>

---

## Availability Date Erasure Chunk

<image src="../../../assets/img/5-Polkadot/bs-erasure-encoding-data-layout.svg" style="width: 1000px">

---

## Availability Date Erasure Chunk

<image src="../../../assets/img/5-Polkadot/bs-erasure-encoding-data-layout-2.svg" style="width: 1000px">

---

## Availability Date Erasure Chunk

<image src="../../../assets/img/5-Polkadot/bs-erasure-encoding-data-layout-3.svg" style="width: 1000px">

Notes:

- Extended the properties from field elements to $n$-field elements
- merkle proof only required across the entire availability chunks
- Any problems? (data size, max encodable size)

---

<!-- .slide: data-background-color="#8D3AED" -->

# Questions

Notes:

- Storage?
- Network Bandwidth?
- Compute required across validators? Who has the hardest time? How to improve?
- ...