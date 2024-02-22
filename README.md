# HyperNova, Protogalaxy and KiloNova: Efficient Multi-Instance Folding Techniques


## Recursive SNARKs and Folding Schemes
SNARKs (Succinct Non-interactive Arguments of Knowledge) are cryptographic protocols used to prove possession of a certain piece of information (commonly referred to as a witness) without revealing the information itself. A SNARK protocol starts by discussing how an arithmetic circuit $C(w,x)$ can be evaluated over a witness $w$ and a public input $x$, enabling a prover to convince a verifier that they possess $w$ such that the output of the circuit is 1, which essentially demonstrates that the pair $(x,w)$ belongs to a certain NP relation $\mathcal R$. SNARKs can be made non-interactive through the use of the Fiat-Shamir heuristic. Several SNARK protocols like Groth16, Marlin, PLONK, and STARKs have been presented so far, each with different trade-offs between the prover and verifier performance, proof size, and security assumptions. However, SNARKs are monolithic, where the prover constructs a single proof for the entire witness $w$.

A non-interactive SNARK can be applied recursively to encode a verifier's algorithm within an arithmetic circuit, incorporating proofs for specific instances $(w',x')$ within the relation $\mathcal R$. The recursion is achieved by encoding the verifier algorithm as part of an arithmetic circuit, including a proof for a particular computational relation. This recursive approach allows for the implementation of Incrementally Verifiable Computation (IVC), Proof Carrying Data (PCD), and parallel or distributed proving systems. More concretely, recursive SNARKs create proofs that can verify other proofs, effectively enabling “proofs of proofs”. This property is quite powerful for constructing scalable blockchain networks that can handle complex operations with verifiable trust. The development of virtual machines, particularly Ethereum Virtual Machines (EVMs) on Layer 2, introduces new requirements for processing non-uniform circuits and delivering zero-knowledge (ZK) capabilities, including the need for minimized proof sizes and the optimization of both the prover and verifier efficiency.

While this opens the door to IVC, PCD, and distributed proving, it poses a significant problem: the verifier in recursive SNARKs is expensive to execute. In other words, recursive SNARKs have extensive computational overhead required to verify the proofs, especially in protocols where verifier complexity scales linearly with the circuit size.

Folding schemes provide sophisticated mechanisms for compressing ZK proofs (ZKPs) by means of reducing the computation cost of the verifier and prover. This article revisits the most recent approaches such as ProtoGalaxy and KiloNova for handling multiple instances, examining the step-by-step process that supports the advanced folding techniques.

Folding Schemes: A Potential Solution to Recursion
Folding schemes are the innovation to address the cost of recursion. Namely, rather than computing a SNARK proof $\prod_i$ for multiple instances $\phi_1, \ldots, \phi_m$, these instances are combined into a single instance $\phi^*$ and a single proof $\Pi$ is generated. The folding process utilizes a deep algebraic framework, introducing a relaxed relation $\mathcal R^{rand}$ and protocols to fold

$R \times \mathcal R^{rand} \rightarrow \mathcal R^{rand}$, and

$\mathcal R^{rand} \times \mathcal R^{rand} \rightarrow \mathcal R^{rand}$.

These schemes reduce the verification load significantly. If the folded instance and witness pair is in $\mathcal R^{rand}$, it implies that the input instances and accumulators were valid with high probability. We enable a folded instance, witness pair to validate the input instances and accumulators with high probability. The key is to take a linear combination of proofs, calculate error terms, and thus limit folding to proofs leveraging linear (homomorphic) commitments. Namely, the fundamental technique of folding involves taking a linear combination of proofs and computing error terms. The beauty lies in the simplicity of verifying each folding step, which allows for deferring final verification and significantly reducing recursion overhead.

Note that due to the linearity requirement, FRI (Fast Reed-Solomon Interactive Oracle Proofs) cannot be used in folding schemes. Despite this limitation, the folding process is straightforward to verify at each step, allowing for the deferral of final verification and thus substantially reducing recursion overhead.

The folding schemes have been first introduced in NOVA for R1CS, but later have been expanded to include other arithmetizations like Sangria for PLONK, and to fold multiple R1CS instances within SuperNova for non-uniform IVC. These schemes are adapted to work with Customizable Constraint Systems (CCS) in HyperNova; and to fold arbitrary and low-degree polynomial maps in ProtoStar, making a significant advancement in the various folding techniques. ProtoGalaxy has enhanced the existing folding schemes that compress the verification process and fold multiple ProtoStar instances, hence reducing the recursion overhead significantly. KiloNova has developed a non-uniform ZK-PCD scheme based on the generic folding approach and has enhanced its performance through various optimization techniques, including circuit aggregation and decoupling.

## High-Level Descriptions of Recent Folding Schemes
Let $\mathbb{F}$ be a prime field which serves as the foundational arithmetic space for cryptographic operations. Let also "commit" be a binding commitment scheme that ensures the integrity and immutability of the data. $Z(X)$ denotes a vanishing polynomial, typically denoted as $X^k-1$, which is used to construct Lagrange polynomials and enforce certain properties in polynomials related to the proof system. $L_i (X)=Z(X)/Z'(r_i)(X-r_i)$ is a Lagrange polynomial for $Z(X)$ for interpolating values in finite fields.

The comparison between HyperNova and ProtoStar reveals specialized folding schemes performed for distinct cryptographic environments:

### HyperNova
HyperNova is a folding scheme tailored for CCS that employs sumcheck protocols to aggregate a new instance by reducing polynomials to scalar equations. Specifically, it verifies a polynomial $g$ and matrices $A_j$ for the equation $g(A_1(\vec{x})_i, A_2(\vec{x})_i, \ldots) = 0$  and then utilizes the sumcheck process to simplify this into a scalar equation.

The verification process requires checking  $\log n$ hash functions, while the prover needs to carry out a degree $d$ sumcheck. This efficiency makes the protocol particularly suited for structured computations.

### ProtoStar
ProtoStar is also a folding scheme for arbitrary polynomials, enabling the folding of CCS without the need for sumcheck during folding. It is a system that supports folding $(\mathcal R$ and $\mathcal R^{{rand}^m}$) and $(\mathcal R^{rand}$ and $R^{rand})$ relations, which provides the necessary framework to fold $(\mathcal R^n, \mathcal R^{{rand}^m}$) by just folding pairs $n + m$ times. ProtoStar does not directly execute a sumcheck; instead, it transforms the instance into a randomized sum version of it and subsequently addresses the claim regarding this randomized sum. This flexibility makes ProtoStar a strong tool for cryptographic proof systems.

It assumes without loss of generality that a function $f: \mathbb F^M→ \mathbb F^N$ is homogeneous (where every term has the same degree) and performs folding by combining the function evaluations over different inputs. Namely, ProtoStar introduces a novel approach to folding by fixing f and utilizing this function within the context of a relaxed relation to simplify the verification process. More concretely, its folding technique is mathematically expressed as:

A pair $(\phi; w)$ belongs to a relation $\mathcal R$ if $f(w) = \vec{0}$ and $\phi = \text{commit}(w)$, where $\text{commit}(w)$ may consist of multiple commitments and challenges. 

To define the relaxed relation (distinct from ProtoStar), additional parameters $e$, $\beta$ are introduced into the instance. A triplet $(\phi, \beta, e; w)$ is in $R^{rand}$ if $\sum_i pow_i(\beta) f_i(w) = e$, where $pow_i(x) = \prod_{j \in S(i)} x_j$ with $j$ in $S(i)$ if the $j$-th bit of  $i$ is 1. It satisfies $pow_i(x, x^2, x^4, \ldots) = x^i$. We can transform $\mathcal R$ to $\mathcal R^{rand}$ by selecting a random $\beta$ and mapping $(\phi; w)$ to  $(\phi, \beta, 0; w)$.

We note that ProtoStar effectively reduces the complexity of verifying multiple instances by encapsulating them into a single, more computationally manageable proof. This scheme offers efficient folding for lookups and non-linear IVC. However, the impact of folding unstructured polynomials compared to structured systems like CCS remains an open question. Additionally, the benefits of folding unstructured polynomials compared to non-linear CCS (often referred to as Plonkish approaches) remain an open question.

### ProtoGalaxy
#### High-Level Description

ProtoGalaxy takes a more direct approach, supporting the folding of multiple instances and thereby reducing the folding overhead. Rather than merging randomized sums through varying challenges, it transforms the accumulator into a claim based on the new challenge. This eliminates the requirement to commit to the power vector and maintains a succinct (logarithmic in n length) representation of the sum's coefficients.

ProtoGalaxy stands out by requiring fewer hashes and utilizing k-MSM (multiscalar multiplications) instead of k scalar multiplications in the circuit. This not only optimizes the computation but also addresses different constraint separation concerns, such as Lagrange-based designs.

There are two types of folding corresponding to two vanishing checks.

##### Technique 1 - mod Z(X) Vanishing Check

* This technique involves encoding a vector $\vec{v}$ as $P(X) = \sum_i v_i L_i(X)$. 
* If $P(X) \equiv 0 \mod Z(X)$, then the vector $\vec{v}$ effectively vanishes, indicating a successful fold. 
* This method is advantageous due to its simplicity and reliance on a single challenge. 
* However, it increases the degree linearly with the number of instances to fold (because $|f| = d$ and $|P(X)| = k$, composing them leads to the degree $kd$), and an extra $\log d$ from Fast Fourier Transforms (FFTs) due to the high-degree polynomial.

##### Technique 2 - Randomized Sumcheck

* By choosing a random vector $\vec{r}$ and verifying that $\sum_i r_i v_i = 0$, this method ensures that the vector $\vec{v} = \vec{0}$. 
* It can efficiently fold multiple accumulators, reducing computational complexity and is suitable for large-scale cryptographic applications. It does not need FFTs or an increased polynomial degree, thereby streamlining the process. 
* However, it requires $\log n$ challenges.

## Protogalaxy Folding Techniques

### Multiple-Instance Folding

ProtoStar’s successor, ProtoGalaxy, extends these capabilities even further. It supports the direct folding of multiple instances, streamlining the process with fewer hashes and multi-scalar multiplications (MSM) in the circuit, offering a new perspective in design space. We have
* 	a randomized instance $(ϕ,β,e;w) ∈ \mathcal R^{rand}$ and
* 	multiple instances $(ϕ_i;w_i )_(i=1)^k ∈ \mathcal R$.

We want to fold these into a new randomized instance $(ϕ^*,β^*,e^*;w^*) ∈ R^{rand}$. Namely, the basic principle of ProtoGalaxy's folding technique is to consolidate multiple instances into a singular, manageable form. 
1. 	This process starts by refreshing the parameter $β$ with new randomness, followed by a lift of all instances using the newly derived $β'$. 
1. 	The instances are then combined with the same randomness using a Lagrange zero check, an important step that ensures the integrity of the fold. 
1. 	A random value is chosen to evaluate the Lagrange and their quotient and calculate a new instance of $\mathcal R^{rand}$.


#### 1. Refreshing β with a new random value
 The refreshing of $\beta$ is a pivotal step in the folding process. During this phase, the verifier selects a random value $\delta$ from the field $\mathbb F$, which is then utilized to interpolate linearly between $\beta$ and $\delta$. The function $F(X)$ is articulated as

- $F(X) = \sum_i pow_i(\beta + X\delta) f_i(w) = e + \sum_i F_i X^i$

where $\delta \in \mathbb F^t$ and $\delta = (\delta^{2^0}, \delta^{2^1}, \ldots, \delta^{2^{n-1}})$. Note that $F(x)$ can be assembled using $O(n)$ field operations.

The elements $F_1, \ldots, F_{\log n}$ are communicated to the verifier, who then provides a scalar $\alpha$. Both the prover and the verifier execute the computation of $F(\alpha) = e + \sum_{i \in [t]} F_i \alpha^i$ and update $\beta_i^*$ as $\beta_i^* = \beta_i + \alpha\delta_i$.



#### 2. Combining Instances with Lagrange Zero Check

To synthesize multiple instances, ProtoGalaxy leverages a mathematical property described by the lemma:

**Lemma:** Lagrange polynomials commute with evaluation up to a quotient:

- $F(wL_0(X) + \sum_{i \in [k]} w_i L_i(X)) = F(w)L_0(X) + \sum_{i \in [k]} F(w_i)L_i(X) + Z(X)Q(X)$

where $F$ is a function, $L_i(X)$ are Lagrange polynomials, $Z(X)$ is the vanishing polynomial, and $Q(X)$ is a quotient polynomial.

Given this, the prover calculates:

- $G(X) = \sum_{i \in [n]} pow_i(\beta^*) f_i(L_0(X)w_i) + \sum_{j \in [k]} L_j(X)w_j$

and finds a polynomial $Q(X)$ such that:

- $G(X) = F(\alpha)L_0(X) + Z(X)Q(X)$

This equivalence is demonstrated by evaluating both sides with $r_j$ where $Z(r_j) = 0$. This allows the scheme to linearly combine the $w$ and $w_i$ values. The prover then computes and transmits the coefficients of Q(X)$  to the verifier, who responds with a random challenge $\gamma \in \mathbb F$.


#### 3.Computing the New Instance

Upon completing the preceding steps, the new instance within $R^{rand}$ is calculable. This updated instance is encapsulated by $e^*$, $\phi^*$, and $w^*$, derived from the evaluations involving the Lagrange polynomial $L_0(\gamma)$ and the quotient $Z(\gamma)Q(\gamma)$.

Concretely, the prover and verifier collaboratively arrive at the new instance as follows:

- The new instance $e^*$ is given by:
$e^* = L_0(\gamma)e + Z(\gamma)Q(\gamma)$

At the conclusion of the protocol:

- The verifier outputs the instance:
$\phi^* = L_0(\gamma)\phi + \sum_i L_i(\gamma) \phi_i$

- The prover outputs the witness:
$w^* = L_0(\gamma)w + \sum_i L_i(\gamma) w_i$

### Protogalaxy Folding 2: Multiple-Instance Folding

ProtoGalaxy also introduces a second folding technique that utilizes a sumcheck protocol. This protocol is advantageous as it does not require FFTs or an increase in the polynomial's degree. It enables the efficient folding of multiple accumulators, thereby further enhancing the verifier's efficiency.

When engaging with multiple accumulators, the challenge lies in the refreshment of $\beta$ to maintain consistency across folds. Directly applying $L_i (X)$ to refresh $\beta$ would yield a polynomial of degree $k(d + log n)$ , which in turn requires degree $k$ polynomial multiplications in the pow function. ProtoGalaxy's solution is to leverage a randomized sumcheck protocol.

#### Using randomized sumcheck
The sumcheck protocol begins by defining the multivariate polynomial $G(b)$ , which includes the Boolean hypercube $K$ with $|K| = \log k$. Here, $K$ consists of bit strings that are essential for the structure of multi-linear extensions and other constructs. The verifier samples $\delta$ to construct $\delta$ as previously described, and $G(b)$ is defined with respect to this sample as:

- $G(b) = \text{eq}(b, \delta) \sum_{i \in [n]} \left( \text{pow}_i \left( \sum_{l \in K} \text{eq}(b, l) \beta_l \right) \right) f_i \left( \sum_{l \in K} \text{eq}(b, l) w_l \right)$

where 

- $\text{eq}(a, b) = \prod_i \left( (a_i b_i + (1 - a_i)(1 - b_i)) \right)$

is diagonal on $K$. Simplifying each $G(j) = \text{eq}(j, \delta) e_j$ for $j \in K$, the verification process is reduced to checking that 

- $\sum_{j \in K} \left( G(j) - \text{eq}(j, \delta) e_j \right) = 0$.

Finally, the sumcheck protocol minimizes verification to a single evaluation of $G(\gamma)$, which is linear in $k$ and $n$.

#### Computing the New Instance

Upon the successful completion of the sumcheck protocol, a scalar $C$ is determined such that $G(\gamma) = C$. Using this scalar, the new instances ($e^*$, $\phi^*$, $w^*$) $\in$ $\mathcal R^{rand}$ are computed with the equality function $\text{eq}$ as follows:

- $e^* = \text{eq}(\gamma, \delta)^{-1} C$ 
- $\phi^* = \sum_{j \in K} \text{eq}(\gamma, j) \phi_j$
- $\beta^* = \sum_{j \in K} \text{eq}(\gamma, j) \beta_j$
- $w^* = \sum_{j \in K} \text{eq}(\gamma, j) w_j$

This process mirrors the earlier version where $L_i(\gamma)$ is approximated by $\text{eq}(\gamma, j)$, which is instrumental for evaluating multivariate polynomials.


## The Verifier's Challenge
In both folding strategies, the verifier is required to check $k$-multi-linear extensions (MLE), where the MLE of a vector $\vec{v}$ is defined as $v(b)=\sum_{a\in K}eq(a, b) v_a$. Leveraging Pippenger's algorithm allows for a more efficient computation than $k$ scalar multiplications. This efficiency gain is particularly significant for IVC systems, where the PCD represents a crucial reduction in the computational overhead associated with folding.


## KiloNova
While various folding approaches have proven robust for IVC systems development, applying them directly to PCD can present significant efficiency challenges. The fundamental architectural differences between IVC and PCD systems result in different proposals. Unlike IVC, which processes two instances sequentially, PCD folds multiple instances simultaneously, leading to an exponential increase in error terms generated during the folding of non-linear equations.

For example, folding two quadratic instances $(w_i, t_i)$ where $w_i^2 = t_i$ for $i = 1,2$ results in a combined instance $(w, t) = (w_1 + \alpha w_2, t_1 + \alpha t_2)$ with a random challenge $\alpha$, failing to meet the condition $w^2 = t$. This necessitates additional error terms like $2w_1 w_2$ for verification.

In the ProtoGalaxy scheme, the count of error terms increases to $O(ds)$ when folding $s$ instances in $d-degree relations, as per Protostar methodologies. However, the critical limitation is the exponential growth in error terms with the folding of multiple high-degree instances, complicating PCD construction.


ProtoGalaxy aims to enhance efficiency in Protostar by leveraging the properties of Lagrange bases, thus keeping recursion overhead manageable in multi-instance scenarios. KiloNova introduces a non-uniform PCD framework with ZK features, derived from universal folding techniques. Inspired by HyperNova, it adapts CCS for linear assertions, eliminating error terms and employing sum-check protocols to streamline CCS relations.

KiloNova outlines a universal folding strategy for multiple instances across various circuits while preserving ZK properties. It significantly enhances the efficiency of non-uniform ZK-PCD systems by integrating optimization methods such as circuit aggregation and decoupling. It constructs a non-uniform multi-folding scheme without error terms and efficiently builds ZK-PCD from this scheme. This is achieved by implementing a relaxed CCS relation focused solely on linear claims related to instance-witness pairings and structures, thereby constructing a non-uniform multi-folding approach that avoids error terms.

## Comparison: HyperNova Protostar ProtoGalaxy and KiloNova
The cryptographic community continually seeks to optimize ZK proof systems, with a particular focus on reducing the verifier's workload. As ZKPs become more prevalent, especially in blockchain applications, efficient verification becomes increasingly crucial. Therefore, comparing various folding schemes is essential. This analysis presents a comparative table that contrasts different folding schemes, highlighting the marginal work required by the verifier for each. Each scheme offers unique advantages, whether in computational efficiency or the robustness of the security model.







# Comparison of Folding Verifiers

| Scheme                           | Language          | Non-Uniform | ZK  | Verifier work                      |
|----------------------------------|-------------------|-------------|-----|------------------------------------|
| HyperNova                        | CCS               | No          | No  | $O(d log n)\mathbb F, d log n CRH, log n RO$   |
| Protostar                        | Degree-d Plonk/CCS| Yes         | No  | $3 \mathbb G, d+O(1)\mathbb F d+O(1)CRH, O(1)RO$|
| Protogalaxy                      | Degree-d Plonk/CCS| Yes         | No  | $d+log n \mathbb F, d+log n CRH, O(1) RO$     |
| Protogalaxy-k instances          | Degree-d Plonk/CCS| Yes         | No  | $kd+log n \mathbb F, kd+log n CRH, O(1) RO$   |
| Protogalaxy sumcheck -k instances| Degree-d Plonk/CCS| Yes         | No  | $log n+d log k \mathbb F, kd+log nCRH, log kRO$|
| Protogalaxy sumcheck -k accumulators| Degree-d Plonk/CCS| Yes      | No  | $log k(log n+d) \mathbb F, k(d+log n)CRH, log kRO$|
| KiloNova                         | CCS               | Yes         | Yes | $1 \mathbb G, O(d log m)\mathbb F, O(log n)CRH, log m+log n RO$|

*Table 1: Comparison of Folding Verifiers where d is the degree of verifier checks, n is the circuit input, for CCS instances with m×n circuit matrices, CRH is collision-resistant hash function, and RO is random oracle.*

The comparison table provides an analysis of the work required by the verifier in different folding schemes, highlighting the efficiency gains of the ProtoGalaxy and ProtoStar schemes over HyperNova. Verifier workload in folding is measured in terms of field operations, hash function checks, and the degree of required sumcheck. Particularly when dealing with multiple instances, the ProtoGalaxy scheme demonstrates a linear relationship between the number of instances and the verifier's work, indicating scalability.

As the design space expands to include multiple instances and accumulators, ProtoGalaxy adapts by introducing sumcheck protocols that compress the verifier's workload effectively. It requires fewer hash checks and can leverage multi-scalar multiplications instead of multiple scalar multiplications, significantly reducing computational demands. The ProtoStar and ProtoGalaxy folding schemes represent a convergence of mathematical elegance and computational efficiency, offering an advanced mathematical framework for efficient verification in Zero-Knowledge Proofs (ZKPs). By leveraging Lagrange polynomials, multi-linear extensions, and a novel approach to defining relaxed relations, these schemes significantly reduce the verifier's workload.

Although ProtoGalaxy has effectively reduced the growth of error terms from exponential to linear asymptotically, it introduces quasi-linear prover complexity due to the computational demands of Lagrange bases. The introduction of these innovative approaches into the world of ZKPs has opened the door to more scalable blockchain systems.

## References
- A curated list of awesome resources related to ZK folding schemes. [https://github.com/lurk-lab/awesome-folding](https://github.com/lurk-lab/awesome-folding)
- Tianyu Zheng and Shang Gao and Yu Guo and Bin Xiao. KiloNova: Non-Uniform PCD with Zero-Knowledge Property from Generic Folding Schemes. [IACR Cryptology ePrint Archive. 2023: 1579](https://eprint.iacr.org/2023/1579.pdf)
- Liam Eagen and Ariel Gabizon. ProtoGalaxy: Efficient ProtoStar-style folding of multiple instances. IACR Cryptology ePrint Archive. 2023: 1106 (2023). https://eprint.iacr.org/2023/1106.pdf
- Benedikt Bunz and Binyi Chen. Protostar: Generic Efficient Accumulation/Folding for Special-sound Protocols. IACR Cryptology ePrint Archive. 2023: 620 (2023). https://eprint.iacr.org/2023/620.pdf
- Abhiram Kothapalli and Srinath Setty. HyperNova: Recursive arguments for customizable constraint systems. IACR Cryptology ePrint Archive. 2023: 573 (2023). https://eprint.iacr.org/2023/573.pdf
- Abhiram Kothapalli and Srinath Setty, Ioanna Tzialla. Nova: Recursive Zero-Knowledge Arguments from Folding Schemes. IACR Cryptology ePrint Archive. 2021: 370 (2021). https://eprint.iacr.org/2021/370.pdf
- https://github.com/arnaucube/protogalaxy-poc/tree/main/src
- https://github.com/ingonyama-zk/super-sumcheck
- Daniel J. Bernstein. Pippenger’s Exponentiation Algorithm. https://cr.yp.to/papers/pippenger.pdf
- Nicholas Pippenger, The minimum number of edges in graphs with prescribed paths, Mathematical Systems Theory 12 (1979), 325–346; MR 81e:05079.
- Nicholas Pippenger, On the evaluation of powers and monomials, SIAM Journal on Computing 9 (1980), 230–250. MR 82c:10064

