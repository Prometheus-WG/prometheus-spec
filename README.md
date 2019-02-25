Prometheus
===

**_Scalable High-load Computing & Machine Learning on top of Bitcoin_**

Maxim Orlovsky<sup>1, 2</sup>, Sabina Sachtachtinskagia<sup>1, 3</sup>  
<sup>1</sup>Pandora Core AG, Switzerland;  
<sup>2</sup>BICA Labs, Switzerland;  
<sup>3</sup>Athens University of Economics and Business, Greece

Censorship resistant decentralised computing is a new topic in distributed systems theory. With the recent rise in popularity of Bitcoin as a censorship-resistant financial and transactional system a new technological field of blockchain and its applications appeared. This work addresses the problem of computational integrity proofs (correctness of the actual computations) made by some non-trusted (anonymous) third party without repeating the whole volume of the actual computing. The issue is usually addressed by use of probabilistically checkable proofs (PCP), however, such simple approach leave untouched the question of coupling the payments for the consumed computational resources with the actual proof. The purpose of our protocol is to scale distributed calculations by obtaining results of nearly perfect (measurable) quality, without the need to repeat calculations or check the computational integrity proofs by the client. This is achieved by providing meritocratic economic incentives to all participating economic agents, so that their interests are aligned with the network's success. As a result, the need for repeat calculations diminishes, and costs of calculations become lower and competitive. Ability to run PCP schemes without trusted checking party provides a way to codify the actual protocol in terms of smart contracts, so it can be used in a very compact form with existing economic blockchain layer. Our approach to protocol design aims at the smallest possible size of the shared system state so it can be implemented with any non-Turing complete smart contract system with storing nothing else then the account balances for the involved parties. For example, the protocol can be implemented with the existing Bitcoin script functionality with only two on-chain transactions per whole protocol computing cycle. In such system the PCP proofs are run the second layer on top of actual Bitcoin blockchain, reducing the footprint, price and increasing scalability for the real-world business cases for the computing.

[Check the full PDF version of the paper](./prometheus.pdf)

![Prometheus Logo](assets/logo.png)
