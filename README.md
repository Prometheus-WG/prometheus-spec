Prometheus
===

**_Censorship-resistant trustless protocol for high-load computing & machine learning on top of Bitcoin smart contracts_**


*Working Draft*

Maxim Orlovsky<sup>1, 2</sup>, Sabina Sachtachtinskagia<sup>1, 3</sup>  
<sup>1</sup>Pandora Core AG, Switzerland;  
<sup>2</sup>BICA Labs, Switzerland;  
<sup>3</sup>Athens University of Economics and Business, Greece


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

Protocol
---

### Setup

Participants:
- Client node `C`: party prepared to pay for the actual computing and provide necessary data for it.
- Worker node `W`: party prepared to perform actual computing for some reward `s`.
- Verifier node `V`: party prepared to verify the actual computing using PCP for some reward `s / z << s`

The client node splits the actual computing task into *batches* suitable for parallel processing in a distributed P2P network.

For each batch, the client selects Client and Verifier nodes on some open market or by auction (the actual selection is not part of this protocol). They agree upon the price of the computing and stakes (see below) in the process of off-chain P2P communications. 

To secure the actual computing Worker and Validator node prepare payments with stakes `w` and `v` correspondingly. All three parties secure costs `s+w+v` under special contract on the Bitcoin blockchain (see section "Smart Contract" below for the contract details) and wait for a necessary number of confirmations (up to their agreement).

### Contract execution

The Worker node receives all necessary data from the client (via p2p channel or by other means, like IPFS) and performs the actual batch of computing with the data after splitting them into a separate **computing steps**. 

For different computing models a step can be defined in such ways:
* *Map-reduce*: a snapshot of input data with the corresponding result for each `map` and `reduce` functions call
* *Inference on pre-trained machine learning model*: each input for the model with the corresponding result produced by the model
* *Training of machine learning model*: weights matrix for each step of the training

Worker node performs actual computations and runs the following non-interactive PCP scheme (see Fig. 1): for each computing step (see definition below) the Worker has to compute the hash value (**commitment**). Commitments are merkelized into a tree. The hash of the Merkle tree root is used as a random oracle to sample some pre-agreed amount of the commitments. For each sampled commitment a proof is constructed, consisting of an index of the source computing step input, its resulting value and the Merkle path to the Merkle tree root. 

[ ] Add PCP scheme figure @dr-orlovsky

These data are sent by Worker node to Verifier together with unlocking transaction (see "Smart contract" below) signed by `W`. Verifier checks the proofs by (a) checking the random oracle and (b) repeating sampled computations against their proofs. If the results are correct, Verifiers signs the unlocking transaction with its private key (`V`) and passes it back to the Client node. If the results are incorrect, the Verifier notifies the Client node and does not sign the unlocking transaction.

The Worker has the right not to sign the received unlocking transaction if he is not satisfied with the results of computation by any reason (the protocol still be secure if it works under assumptions provided in "Economic incentives" section below). If the Worker decides to sign the transaction, he sends it over to the Bitcoin network to settle the payments.

Smart contract
---

### Payment settlement schemes

There are 8 possible combinations of the Worker, Verifier and Client node signatures on the unlocking transaction:(`2^3=8`), out of which 3 are nonsense/invalid (since they include either Client of Verifier signature, which does not make economic sense: there is no reason to pay if the actual computations were not made). Thus, the unlocking script of the Prometheus-type smart contract defines 5 scenarios with corresponding rewards:

Scenario               | Signatures | W txout | V txout | C txout | Script type
---------------------- |:----------:|:-------:|:-------:|:-------:| -----------
Worker unresponsive    | none       |         |         |         | CLTV return of funds to `C`
Everything correct     | W+V+C      | s+w     | s/z+v   | 0       | 3/3 multisig
Client declined to pay | W+V        | w+x%    | v       | s-x%    | 2/2 multisig
Faulty Verifier        | W+C        | s+w     | 0       | 0       | 2/2 multisig
Faulty Worker          | W          | 0       | v+w+s/z | 0       | Normal

### Contract script

Let :
- `W` be a signature of a worker with stake `w`, 
- `V` be a signature of a verifier with stake `v`, 
- `C` be a signature of client,
- `s` the agreed payment from the client for a successful contract execution,
- `t` be an agreed maximum time for the contract execution.

All the stakes (`w+v`) and client payment (`s`) are deposed as tree *txins* with a single *txout*, locked with P2SH script.

Transaction script for hashing into `ScriptPubKey`:
```
OP_DEPTH
<3>
OP_VERIFY
OP_IF
    OP_DROP2
    3 <W> <V> <C> 3
    OP_CHECKMULTISIG
OP_ELSE
    OP_DROP
    <2>
    OP_VERIFY
    OP_IF
        OP_DROP2
        2 <W> <V> 2
        OP_CHECKMULTISIG
        OP_NOTIF
            2 <W> <S> 2
            OP_CHECKMULTISIG
        OP_END
    OP_ELSE
        OP_DROP2
        1 <W> 1
        OP_CHECKMULTISIG
        OP_NOTIF
            1 <C> 1
            OP_CHECKMULTISIG
            <t>
            OP_CHECKLOCKTIMEVERIFY
        OP_END
    OP_END    
OP_END
```

Aligning of economic incentives
---

Let `p` be the probability of cheating detection, `s` the standard payment
for work and `c` the cost to produce the calculation.

Let the standard collateral stake, lost when cheating is detected, be `w`
for Worker node and `v` for Verifier node, where `v \geq w`.

In order for the network to operate, producing nearly perfect results in
equilibrium, cheating must be unprofitable.

### Assumptions

[ ] TBD @sabina-sa

### Prevention of worker cheating
*Collateral stake size vs probability of detection trade-off*

Let's calculate the expected profits from cheating. The payoffs for each
world condition are given in the following Table.

Decision to cheat | Payoff
----------------- |:------:
True node | `s-c`
False node (undetected) | `s`
False node (detected) | `-w`

The expected payoff when not cheating is `s-c`, i.e. standard payment for
the assignment, minus the incurred cost.

The expected payoff when cheating depends on the probability of detection `p`%
. Hence it is `(1-p)s+p(-w)`, i.e. the expectation of standard payment `s`
without any doing work minus the expectation to lose one's stake collateral
deposit `w`.

Hence the expected payoff for the strategy 1 is as follows:

Decision to cheat | Expected payoff
----------------- |:---------------:
True node | `s-c`
False node, dissolve if detected | `(1-p)s+p(-w)`

Therefore, cheating is not profitable when `s-c>(1-p)s+p(-w) \Longrightarrow
p(s+w)>c`

Observe that the result depends on all four parameters. It depends
positively on the probability of detection `p`, the standard payment `s` and
the stake `w`; it depends adversely on the cost of calculations `c`.

In order to quantify the above results, we need to make reasonable
assumptions about the relation between payment `s` and cost `c`. This
depends on the "markup" (profit margin) `\frac{s-c}{c}`. If `\frac{s-c}{c}=0`
then this markup is 0\%, therefore, `s=c`. If `\frac{s-c}{c}=1` then this
markup is 100\%, therefore `s=2c`.

The Table below uses the formula `p(s+w)>c` and shows how high the stake `w`
need to be, relative to calculation cost `c`, in order to deter cheating.

 `p` | `\frac{s-c}{c}` | `w`
:---:|:---------------:| ---:
`0.02` | `0` | `49c`
`0.02` | `1` | `48c`
`0.05` | `0` | `19c`
`0.05` | `1` | `18c`
`0.1` | `0` | `9c`
`0.1` | `1` | `8c`
`0.2` | `0` | `4c`
`0.2` | `1` | `3c`

Therefore we find that e.g. a combination of Worker stake `w=9c` and
repetition of calculations (i.e. probability of detection) `p=10\%` would be
sufficient to make cheating unprofitable, regardless the markup level.

Note that the results are not very sensitive to a change in the markup.
Instead, they are quite sensitive to a change in probability of detection `p`%. 
This is because the biggest impact on incentives comes from losing the
collateral stake rather that from losing the payment for work. Hence a
simplified, practical rule of thump `w \geq \frac{c}{p}` could be used. The
tradeoff is between increasing the probability of detection `p` by costly
repetition of calculations, versus having too high a stake that workers
cannot afford.

If `c` is unobservable on the market, we can logically assume that `c\leq s`
(otherwise the worker would operate at loss) and therefore we can use `s` as
proxy for maximum `c`. Therefore, the rule of thumb becomes `w \geq \frac{s}{p%
}`.

### Prevention of Verifier(s) cheating

The probability of detection `p` is independent of the number of Verifiers
that work on a task; it depends only on the percentage of work
re-calculated. Any additional Verifier that repeats calculations increses
the customer's cost sharply. However, increase in the number of Verifiers
could potentially help to prevent collusion between the Worker and the
Verifier (two Verifiers would be sufficient to create a Prisoner's Dilemma).
Collusion can happen if the Worker could communicate to the Verifier the
result that the Worker intends to send to the network. Knowing that result
(and assuming that the Verifier believes the Worker's declaration), the
Verifier could send the same result without doing the calculations. 

Since we cannot predict the futuristic technologies that can facilitate collusion
(like smart contracts and automated prediction markets), it is easier to
approach the problem by calculating the budget that the Worker node has to
spend for facilitation of collusion. The Worker's maximum gain from getting
paid without procuring any cost is `s`. However, the gain of
Verifier from reporting the Worker's results as faulty is `w` (the
confiscated worker stake), where `w > s`. In addition, as long as `v > w`, it is
easy to setup bounty programs there Worker could benefit from luring the
Verifier to collusion (by sacrificing `w)` and then reporting him to the
customer's arbitration to gain `v`. The Prisoner's dilemma already exists
between the Worker and the Verifier, without the need for a second Verifier.
Hence a rational Verifier will not collude with the Worker, unless of course
they both happen to belong to the same actor. But any actor large enough to
benefit consistently from such a coincidence is a large enough player to
have its incentives aligned with the network's success and would likely not
undermine it by cheating.

### Possible corruption scenarios

[ ] TBD @sabina-sa

References
---

[ ] TBD @dr-orlovsky @sabina-sa
