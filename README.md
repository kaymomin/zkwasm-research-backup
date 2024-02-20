# Lookups and LogUp 

## What is Lookup?
A lookup refers to the process of searching for or retrieving specific values from a table 𝑇. The table 𝑇 consists of distinct values arranged in rows, denoted as 𝑖 = 0, … , 𝑁 − 1. The values in the table represent permissible or legal values for a certain variable. A list of lookups 𝐹 is given, represented by $f{_j}$, where 𝑗 = 0, … , 𝑚 − 1. These lookups are instances of the variable generated during the execution of a particular program. The goal is to show that all the lookups in 𝐹 are contained in the table 𝑇, expressed as 𝐹 ⊆ 𝑇. In other words, given sequences < $w{_i}$ > and < $t{_j}$ >, < $w{_𝑖}$ > ⊆ < $t{_j}$ > as sets if and only if ∃ < $m{_j}$ > such that: 

$∏{_𝑖}$(X - $w{_𝑖}$) = $∏{_j}$(X - $t{_j}$)$^m$$^{_j}$,

The conditions 𝑚 < 𝑁 and, in most cases, 𝑚 ≪ 𝑁 signify that the number of lookups is significantly smaller than the number of distinct values in the table. This is a typical scenario, where the set of possible values 𝑁 is much larger than the specific instances of the variable observed during the program's execution 𝑚.

## Multiset Equality Checks

The Grand Product Check involves examining given commitments to polynomials 𝑓 and 𝑔 over a finite field 𝐹 and a subset 𝐻 = {$x{_1}$, … , $x{_n}$} ⊂ 𝐹. This check ensures that the product of values of 𝑓 and 𝑔 over 𝐻 aligns, determining whether:

$∏{_𝑖}{_∈}{_[}{_𝑛}{_]}$ $a{_𝑖}$ = $∏{_𝑖}{_∈}{_[}{_𝑛}{_]}$ $b{_𝑖}$

where 𝑎𝑖 = 𝑓($x{_𝑖}$) and $b{_𝑖}$ = 𝑔($x{_𝑖}$). Grand Product Check is useful because with one randomness it could be transformed into a more powerful primitive known as the “the multiset equality check”. Multiset equality check involves the comparison of two sequences, denoted as < 𝑤 >= ($w{_𝑖}$, … , $w{_n}$) and < 𝑡 >= (𝑡, … ,$t{_n}$), to verify if they are permutations of each other. Unlike standard set equality, multisets allow for repeated elements. Two sequences 𝑤 and 𝑡 are permutations of one another if and only if:

$∏{_𝑖}$(X - $w{_𝑖}$) = $∏{_j}$(X - $t{_j}$)

over a field 𝐹. The equation essentially states that the product of differences between 𝑥 and the elements of the first sequence is equal to the product of differences between 𝑥 and the elements of the second sequence. Bayer and Groth's 2013 work focus on the reduction of polynomial identity to the grand product form using a random variable α chosen from a finite field 𝐹. This grand product check is expressed as:

$∏{_𝑖}$(a - $w{_𝑖}$) = $∏{_j}$(a - $t{_j}$)

using a random 𝛼 ∈ 𝐹. By introducing a random variable 𝛼, the Schwarz-Zippel Lemma implies that the grand product check fails with high probability unless < $w{_𝑖}$ >, < $t{_j}$ > are multiset equal.

## Standard Permutation Check through Polynomial Commitment 

![screely-1708413688427](https://hackmd.io/_uploads/rknGQA-3p.png)


Let us $L{_k}$ denotes the 𝑘-th Lagrange interpolation. These conditions serve as checks to ensure the integrity and correctness of the polynomial commitments and evaluations in the protocol. The commitment and opening steps are designed to convince the verifier that the prover knows valid evaluations of the polynomials at specific points (𝛾 and 𝑤𝛾) without revealing the entire polynomials. The verifier then verifies these claims based on the received information. $L{_4}$(𝑥)(𝑍(𝑥) − 1) = 0 ensures that the prover follows the protocol correctly, and the second equation involves a relationship between the committed polynomials 𝑍(𝑥), 𝑓(𝑥), and 𝑡(𝑥) at a specific point 𝑤𝛾.

### Example:
Given the following table of two functions evaluated at 𝑤$^0$, 𝑤$^1$, 𝑤$^2$, 𝑤$^3$:

![image](https://hackmd.io/_uploads/BkIVd0-3p.png)

To prove that 𝑓 and 𝑡 are the permutation of each other, we do the followings.

Let’s say $L{_4}$(𝑤$^0$) = 0, $L{_4}$(𝑤$^1$) = 0, $L{_4}$(𝑤$^2$) = 0, $L{_4}$(𝑤$^3$) = 1 where $L{_4}$ denotes the 4-th Lagrange interpolation. Hence, the expressions for 𝑧(𝑤$^𝑖$) involve a parameter 𝛽. Namely,

![image](https://hackmd.io/_uploads/SJZfFAZ3p.png)

${L{_4}(𝑥)(𝑧(𝑥) − 1) = 0}$  and ${𝑧^′ = 𝑧.{𝑓^′+𝛽 \over 𝑡^′+𝛽}}$ are the constraints where (𝑧’, 𝑓’,𝑡’) represent the next step of (𝑧, 𝑓,𝑡) accordingly (i.e., grand product).

## Plookup (Permutation Lookup)
Plookup has been designed to improve the efficiency and scalability of zero knowledge proofs by enabling a more efficient way to verify that elements belong to a set or to verify the correctness of various operations (like arithmetic operations). It helps to reduce the computational and storage requirements for these proofs, making them more practical for use particularly in blockchain applications.

![image](https://hackmd.io/_uploads/ryj_oRWha.png)

Incorporating a sorted union operation into Plookup extends its functionality to efficiently handle scenarios where inputs need to be verified against multiple lookup tables or when ensuring the uniqueness of elements across these tables is crucial. This addition can significantly enhance the protocol's performance for zero-knowledge proof systems.

![Untitled](https://hackmd.io/_uploads/BkNvh0W3T.png)

The prover's computational complexity is stated as 𝑂(𝑁 𝑙𝑜𝑔𝑁) field operations, where 𝑁 represents the size of the public lookup table 𝑇. This computational complexity indicates that the efficiency of the protocol is related to the size of the public table 𝑇, making it relatively efficient even as the table size grows. The statement also highlights the protocol's capacity to be generalized to accommodate multiple lookup tables and vector lookups.

## Flookup (Function Lookup)
Flookup is a variant or an extension of the Plookup, aimed at enhancing the efficiency and capabilities of SNARKs. While Plookup focuses on efficiently verifying the existence of elements or the correctness of operations against a public lookup table, Flookup extends this concept to more complex scenarios involving function evaluations and the handling of dynamic or more sophisticated data structures
within zero-knowledge proofs. 

The main objective of Flookup is to provide an efficient and succinct proof that the values of a committed polynomial are indeed part of a large table. The protocol leverages pairings to extract the vanishing polynomial of a relevant subset of the table. Flookup introduces a new polynomial IOP (Interactive Oracle Proof) for the proofs which involve interactions between the prover and verifier, with the prover providing responses to verifier queries.

The protocol involves an initial preprocessing step with a complexity of 𝑂(𝑁𝑙𝑜𝑔$^2$ 𝑁) where the lookup table is generated, the table is encoded, and polynomial commitments are calculated. This preprocessing step is where pairings are computed, and other necessary information is prepared for subsequent proofs. After the preprocessing, the prover operates in quasi-linear time, with a complexity of 𝑂(𝑚𝑙𝑜𝑔$^2$ 𝑚), where 𝑚 represents the number of elements or operations the prover is working with in the context of the proof. This represents a significant improvement over previous quadratic
prover complexity. The efficiency is particularly notable when considering large domains. Namely, the quasi-linear complexity ensures that the prover's workload increases only slightly faster than the size of the dataset, making Flookup scalable and practical for large-scale applications. 

## LogUp
LogUp is a new type of a lookup argument based on the principle of logarithmic derivatives. It employs logarithmic derivatives to transform the verification of set inclusion into a check for the equality of rational functions, which significantly reduces the computational requirements. LogUp utilizes the logarithmic derivative to transform products into sums, leveraging the relationship between zeros of a polynomial and the poles of its logarithmic derivative. 

Therefore, it is more efficient compared to multivariate Plookup variants. It also performs favourably against Plookup's bounded multiplicity optimization for large batch sizes and can be extended to vector-valued lookups and adapted for range proofs.

## Logarithmic Derivative

The logarithmic derivative of a function 𝑝(𝑥) is denoted as 𝐷𝑙𝑜𝑔(𝑝(𝑥))and is defined as the ratio of the derivative of 𝑝(𝑥) to 𝑝(𝑥). Namely, 


${𝐷_{𝑙𝑜𝑔}(𝑝(𝑥))= {𝑝′(𝑥) \over 𝑝(𝑥)}.}$


This concept simplifies certain mathematical transformations by: 

- Derivative of a logarithm of a function ${𝑓(𝑥): [log(𝑓(𝑥))]’ = {1 \over 𝑓(𝑥)} ∗ 𝑓’(𝑥)}$
- Turning products into sums: The logarithmic derivative of the product of two functions is the sum of their logarithmic derivatives: 𝐷$_{𝑙𝑜𝑔}$(𝑝(𝑥)𝑞(𝑥)) = 𝐷$_{𝑙𝑜𝑔}$(𝑝(𝑥)) + 𝐷$_{𝑙𝑜𝑔}$(𝑞(𝑥))

## What is LogUp?

Given a polynomial 𝑝(𝑥) = ∏ (𝑋 − 𝑧𝑖) 𝑁 𝑖=1 is transformed into a sum of reciprocals ∑ 1 𝑋−𝑧𝑖 𝑁 𝑖=1 , where each term has poles corresponding to the zeros of the original polynomial with the same multiplicity.

