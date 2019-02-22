Protocol
===

Setup
---

The protocol design includes the following types of participants:
- Client node `C`: party prepared to pay for the actual computing and provide necessary data for it.
- Worker node `W`: party prepared to perform actual computing for some reward `s`.
- Verifier node `V`: party prepared to verify the actual computing using PCP for some reward `s / z << s`

The client node splits the actual computing task into *batches* suitable for parallel processing in a distributed P2P network.

For each batch, the client selects Client and Verifier nodes on some open market or by auction (the actual selection is not part of this protocol). They agree upon the price of the computing and stakes (see below) in the process of off-chain P2P communications. 

To secure the actual computing Worker and Verifier node prepare payments with stakes `w` and `v` correspondingly. All three parties secure costs `s+w+v` under special contract on the Bitcoin blockchain (see section "Smart Contract" below for the contract details) and wait for a necessary number of confirmations (up to their agreement).

Contract execution
---

The Worker node receives all necessary data from the client (via p2p channel or by other means, like IPFS) and performs the actual batch of computing with the data after splitting them into a separate **computing steps**. 

For different computing models a step can be defined in such ways:
* *Map-reduce*: a snapshot of input data with the corresponding result for each `map` and `reduce` functions call
* *Inference on pre-trained machine learning model*: each input for the model with the corresponding result produced by the model
* *Training of machine learning model*: weights matrix for each step of the training

Worker node performs actual computations and runs the following non-interactive PCP scheme (see Fig. 1): for each computing step (see definition below) the Worker has to compute the hash value (**commitment**). Commitments are merkelized into a tree. The hash of the Merkle tree root is used as a random oracle to sample some pre-agreed amount of the commitments. For each sampled commitment a proof is constructed, consisting of an index of the source computing step input, its resulting value and the Merkle path to the Merkle tree root. 

[ ] Add PCP scheme figure @dr-orlovsky

These data are sent by Worker node to Verifier together with unlocking transaction (see "Smart contract" below) signed by `W`. Verifier checks the proofs by (a) checking the random oracle and (b) repeating sampled computations against their proofs. If the results are correct, Verifiers signs the unlocking transaction with its private key (`V`) and passes it back to the Client node. If the results are incorrect, the Verifier notifies the Client node and does not sign the unlocking transaction.

The Worker has the right not to sign the received unlocking transaction if he is not satisfied with the results of computation by any reason (the protocol still be secure if it works under assumptions provided in "Economic incentives" section below). If the Worker decides to sign the transaction, he sends it over to the Bitcoin network to settle the payments.

