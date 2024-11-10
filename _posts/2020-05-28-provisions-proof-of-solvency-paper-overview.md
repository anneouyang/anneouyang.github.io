---
layout: post
title: "Provisions: Privacy-Preserving Proofs of Solvency for Bitcoin Exchanges Paper Overview"
author: "Anne Ouyang"
# categories: blog
# tags: [cryptography, cryptocurrency, paper-reading]
image: 
---



Managing cryptographic keys can be difficult for users who hold digital assets, so exchanges are often used for an user experience similar to banking. Solvency is one factor in assessing an exchange's credibility -- users can determine whether the exchange's assets can cover its liabilities. [Provisions](http://www.jbonneau.com/doc/DBBCB15-CCS-provisions.pdf) is a privacy-preserving proof of solvency which proves that the total amount of users' assets is less than the exchanges reserves without revealing information about the customer's holdings, the total value of the exchange's holdings, and eliminates the possibility of collusion among different exchanges. 

*Provisions* consists of three main protocols:

1. **Proof of assets**: The exchange assembles a set of public keys and yields a Pedersen commitment to each public key in the set, and a homomorphic addition yields a commitment to its total assets. Through an interactive zero-knowledge proof, the prover convinces the verifier that for $$i \in [1, n]$$ when $$s_i = 1$$, the exchange knows the private key $$\hat x_i \in \mathbb{Z}_q$$ such that $$g^{\hat x_i} = y_i$$. The proof is an honest-verifier zero-knowledge and can be made non-interactive using the Fiat-Shamir heuristic. The verifier then computes $$\Pi_{i=1}^{n} p^i$$ to ensure that the sum of the balances is equal to the committed value without knowing which public keys the exchange has the corresponding private keys to. This proof size is linear in the size of the anonymity set $$n$$. 
2. **Proof of liabilities**: Every customer should have an entry in a clash-attack-resistant list of liabilities, and the apparent liabilities of the exchange should be greater than or equal to its actual total liabilities. To ensure that there are no negative numbers, the sum of the total liabilities should not exceed the order $$q = 2^{256}$$ of the group $$G = secp256k1$$. A range proof that proves the account balance is at most 51-bits long by a bitwise commitment is used to enforce this. A fresh identifier (a commit to the username) is generated for each customer by applying a collision-resistant hash function to the concatenation of the username and a random nonce. A customer can check that its entry is included in *Liablist* with the correct balance, and a public auditor will verify all commits for all users. The proof size is linear in the number of customers $$c$$. 
3. **Proof of solvency**: A difference between assets and liabilities is computer homomorphically from the two protocols above. If $$Z_{\text{assets} - \text{liabilities}}$$ is a commitment to 0, the proof is a simple Schnorr ZK proof of knowledge of $$k$$ where $$Z_{\text{assets} - \text{liabilities}} = g^0h^k$$. If fractional reserve banking is used, a modified balance can be used in place of the true balance. 

To prevent cabals of insolvent exchanges from colluding, a list $$L$$ of the public keys but computed using the base $$w$$ instead of $$g$$ is published, and auditors can check if the lists of different exchanges are disjoint ignoring the identity element. The correctness of $$L$$'s construction is proved through a Neff-like ZKP. 

To ensure that different anonymity sets between audits do not leak information regarding the exchange's holdings, the different sets used should be similar and grow over time with new addresses. 

If the exchange chooses to fraudulently omit some liabilities, assuming the exchange has $$U$$ users and $$F$$ entries and a random subset $$A \subset U$$ users perform audits, the probability of not being caught is $$\frac{U-F \choose A}{U \choose A}$$, which is upper-bounded by $$min[(1 - \frac{A}{U})^F, (1 - \frac{F}{U})^A]$$. 

The inherent limitation of this work is that a proof of solvency does not guarantee honest behavior in the future; nevertheless, frequent monitoring of the financial health of exchanges can help mitigate losses through early detections. 

Some future work includes:

- Extending *provisions* to support proof of ownership of unused pay-to-pub-key hash, unused pay-to-script-hash address, or multisig addresses through using zk-SNARKS
- Developing and analyzing a heuristic for forming an anonymity set
- Designing a proof-of-assets protocol that is compatible with HSMs that only perform complete ECDSA signatures and do not support an isolated addition with the key
- Taking into account the age of each UTXO to allow exchanges to demonstrate stability