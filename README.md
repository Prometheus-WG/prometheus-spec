Prometheus
===

**_Censorship-resistant trustless protocol for high-load computing & machine learning on top of Bitcoin smart contracts_**


*Working Draft*

Maxim Orlovsky<sup>1, 2</sup>, Sabina Sachtachtinskagia<sup>1, 3</sup>  
<sup>1</sup>Pandora Core AG, Switzerland;  
<sup>2</sup>BICA Labs, Switzerland;  
<sup>3</sup>Athens University of Economics and Business, Greece

![Prometheus Logo](assets/prometheus.svg)

Introduction
---

The progress in the field of computing led to the appearance of cloud computing platforms. The success of cloud computing was related to the economy of scale phenomenon, enhanced by the exponentially increasing amounts of produced data. However, it has created systematic risks and fragility, namely privacy risks, possibilities of totalitarian control, surveillance, single points of failure and possibilities of censorship. It has put at risk the further development of machine learning technology, started to be monopolised together with the access to big data. [The #FreeAI Manifesto](https://manifesto.ai), had declared an initiative to oppose this dramatic trend, and this work aims at the development of the technology stack, which can address the issue.

First, let's split the problem of censorship-resistant computing into parts:
1. Finding and buying information (big data, machine learning models) in a censorship-resistant way.
2. Owning and keeping information (big data, machine learning models) private (with possible plausible deniability).
3. Performing high-load computing on big data in anonymous and censorship-resistant and (desirable) private manner (for the cases when personal computing resources are not enough to perform the actual computation).

Here we address only the third matter. The first two matters can be addresses with many other existing initiatives, such as dark anonymous markets with escrow, cryptography technologies, zero-knowledge, distributed storage and data transfer (Torrent, IPFS etc.).

The technical problem we need to address here is how to prove computational integrity (correctness of the actual computations) made by some non-trusted (anonymous) the third party without repeating the whole volume of the actual computing. This issue is usually addressed by use of Probabilistically Checkable Proofs (PCP) [], however, they leave untouched the question of coupling the payments for the consumed computational resources with the actual proof. Here we aim to utilize existing Bitcoin smart-contracts (Bitcoin script) functionality to be coupled with PCP proofs at the second layer on top of actual Bitcoin blockchain with just two on-chain transactions, reducing the footprint, price and increasing scalability for the real-world business cases for the computing. Furthermore, the solution can work on top of the recently proposed [Typhon sidechains](https://github.com/dr-orlovsky/typhon-spec) giving even better scalability without putting a single transaction on-chain for multi-contract computing cases.

Here we analyse parallelizable categories of high-load computing, including (but not limiting to) map-reduce tasks and inference on pre-trained machine learning models (training of machine learning models can be converted down to parallelizable case with existing federated learning solutions []).

This document consists of three main parts:
1. [Description of game model in terms of game theory](./GameModel.md) – *nearly complete*
2. [Overall protocol design for implementation of the developed game model](./Protocol.md) – *outdated, will be updated soon basing on a new game model developments*
3. [Implementation of protocol using Bitcoin-script based smart contracts and state channels](./SmartContracts.md) – *outdated, will be updated soon basing on a new game model developments*
4. [Wire protocol for peer-to-peer communications for running state channels](./WireProtocol.md) – *not ready yet*
5. [Solutions to a specific problems in implementing particular machine learning algorithms](./Issues.md) – *not ready yet*