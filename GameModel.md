The game is as follows

There are 3 initial parties: Client (C), Worker (W), Verifier (V)

The Client comes with a take-it-or-leave-it offer that Worker and Verifier can agree to sign. The offer is a contract to do calculations (Work) for a Payment, which is split in a predetermined way between W and V. The Worker shall calculate all the Work, while the Verifier shall calculate only a small portion of Work that is called Sample (described more fully at Stage 2). 

According to the contract, the Payment, the Worker Collateral Stake (w) and the Verifier Collateral Stake (v) are deposited to Escrow, to be released under certain conditions described in this text. Since calculations incure costs (hardware, electricity), the incentive to report false results without doing actual calculations is countered by providing collateral stakes that can be slashed if cheating is detected. It is economically logical that v is larger or equal to w, where the case w=v has certain merits of symmetry against attacks on stake. It is also economically logical that Verifier's work should be more expensive (per unit of calculations). This is because Verifier's stake is locked for approximately the same time as Worker, but Verifier's total reward is less than Worker's total reward (since much less work is done).

The first taker of the contract will become Worker. However, the Verifier should be randomly chosen between a sufficiently large pool of volunteers who have made an Initial Deposit (not necessarily equal to v, can be smaller), so as to reduce the possibility of collusion between W, V or C, V.

The full execution of the contract is as follows:


Stage 1: Selection of Worker
---

The Client and the Worker come to agreement about contract terms. The contract for Work is signed by (at least) C, W - this ends stage 1.

Until the contract is signed, there are no further stages.


Stage 2: Worker's calculations of Work
---

The Worker proceeds to calculate the Work. Then, a part of ready Work is chosen as a Sample for verification purposes (according to a known tech process not described here). The Sample is chosen in a way that the Worker does not know which part of Work will be checked and therefore cannot choose which part of Work to calculate selectively. An economically feasible size of such Sample is between 5% and 20% of Work. The Sample result is reported to the Client - this ends stage 2.

If the Worker fails to send the Sample result by a certain time, then he is penalized (not necessarily with a full stake w), and a replacement Worker can take this contract. Alternatively, if that it easier, the contract could be dissolved and everyone except the Worker receives full refund of deposits, while the Worker is penalized (not necessarily with a full stake w). This penalty prevents the stalling attack. The money from penalty could be burnt or given to the Client (and/or Verifier, if he was already chosen) as a compensation for wasting time.

If the Worker sees that data are invalid and cannot be used for calculations, then there are various options to deal with the problem. One easy option is to allow the Worker to proceed as normal with the contract, and the Verifier and Arbiters should consider any results reported by the Worker as correct, and Client must give the full amount of Payment for this Work. This option has transparent design and should lead to a provable Nash equilibrium. Other options, in my opinion, are less solid and should be considered only if the first option is impractical for "marketing" reasons (it punishes Client's mistakes harshly).

The second option is to introduce a stage 1.5 which allows the Worker to back off from the contract after seing the data (if this is allowed without penalties, it makes possible a stalling attack by the Worker, but if we add a small penalty, it makes possible a flooding attack by the Client, so neither solution is perfect). Another option is to have Worker and Verifier both agree on poor quality of data, in which case the Client can receive refund of Payment minus a small penalty (while if Verifier does not agree with Worker on poor quality of data, then either Arbitration, or the Worker can back off from the contract with some penalty). Whether the Client should be entitled to start Arbitration over the quality of data is questionable: it creates a tradeoff between burdening the Arbiters with the trivial task of data checking (if C, W, V do not all agree) and assigning minor penalties to Client that cannot be protested in Arbitration.


Stage 3: Selection of Verifier
---
_(not strictly in this order, can be inserted earlier)_

Potential Verifiers make Initial Deposits (not necessarily equal to v), declaring that they agree with the contract terms. The actual Verifier is chosen randomy among them and signs the contract for Work - this ends stage 3. Note thah the gathering and /or selection of Verifier could have been done at any time earlier; the timing is not critical, but it affects the length of time that the Verifier's stake is locked. There is a minor practical trade-off: choosing a Verifier late rather than early allows to minimize the time that the Verifier's stake is locked, however it creates more uncertainty whether the Verifier will be found in time.

If the chosen Verifier fails to sign the contract by a certain time, then he is penalized with the size of his Initial Deposit. This penalty prevents the stalling attack. The money from penalty could be burnt or given to the Client and/or Worker as a compensation for wasting time. 

An alternative approach to the process of Selection of Verifier is to make the choice of Verifier's be totally Client's responsibility. That would affect the incentives of participants in many ways. It would take away the safeguards that come from the fact that parties were not initially related to the Client and therefore not colluding with the Client by default, and that such a collusion was relatively rare. If such a collusion is trivial, then the main remaining safeguard would be the Arbitrage. It is not an impossible setting, but it lays much greater responsibility on the Arbitrage and gives more incentives for Arbiters corruption. As a side note, it may be hard for the honest Client to pick an honest Verifier, since the Client is not well versed in random procedures and is likely to choose whoever comes first, or from a small initial pool, and those could more easily be Worker's collaborators.

Stage 4: Verifier's calculations of Sample
---

The Verifier receives the information how to calculate the Sample. The Verifier calculates the Sample and reports to the Client. Hence, the Client gets the information whether the Worker's results are considered correct by the Verifier - this ends stage 4. Note that it is not critical whether the Verifier received the Sample value calculated by the Worker. Both settings are possible: a) where the Verifier reports his own Sample result (without knowing the Worker's Sample result) and b) where the Verifier reports only a binary agreement or disagreement with the Worker's Sample result. However, the setting (a) is better, as it makes collusion between W and V more costly.

If the Verifier fails to report to the Client by a certain time, then he is penalized (not necessarily with a full stake v, however it should be a significant penalty, not a minor one). This penalty prevents the stalling attack and other combined attacks. In this case, a replacement random Verifier needs to take over the contract, perhaps one that is chosen initially as a replacement. The money from the penalty could be burnt. If the Verifier's failure to report to the Client can be detected automatically then some of penalty the money could given to the Client and/or Worker  as a compensation for wasting time. However, if the Verifier's failure to report to the Client is not automatic, but needs to be confirmed by the Client, then it wiser not to give to Client any monetary rewards for penalising the Verifier, and the Verifier should be able to appeal to Arbitrage.

More generally, whenever and party receives money extracted from the stake of another party, this creates incentives to attack by misreporting another party's failure. This is why any significant portions of stake taken from W or V should never be fully split between C, W, V - a large part of the penalty money should be burnt (unless used to pay for Arbitration), while minor compensations can be given. The stakes exist mostly to provide payment for Arbitration, if such a need arises. Minor compensations to C, W, V may be useful to offset attacks that waste time of network participants (I call these "stalling attacks").

If inserting a replacement Verifier into a contract is technically very hard, there is another possibility. The contract could be dissolved. In that case, the Worker is paid for the Work fully, by money from Verifier's penalty. The Client receives back his full Payment plus the Work results, unverified. 

As a sidenote: a question may arise, if the Client, having received the Sample calculations by two parties (which in this case would be two Workers) can be entrusted with reporting whether the results agree. That would make the Verifier's role redundant. However, in my opinion, the one who produces the Sample calculations checking (whether we call him V or "another W") should also be the one who reports whether it agrees with the initial Worker's result. Or (even better) if this can be done automatically by comparing the two values. The Client, of course, can easily understand whether two values, reported to him, agree or not - however, preferrably he should not be the one who reports this. Adding agents (including the Client) to the chain of information flow would complicate incentives and create "broken phone" situations, where information changes as it passes through agents and it is hard to find who has modified it.

Stage 5: Payment release and obtaining of results by Client
---

Having already received the Sample results and the Verifier's report, the Client chooses either to release the Payment voluntarily and receive the full Work results, or to ask to back off and not receive the full Work results - making the choice ends stage 5. 

Note that the ultimate responsibility of checking the Sample quality is on the Client. The Client may, for example choose to do the check himself, or outsource to specialists. However, the usefulness of a Verifier becomes obvious here. There are reasons why the Client many find it more practical to proceed without centralized checking when W, V agree. Some possible reasons are: a) costs of calculations b) there could be cases where having a small percentage of faulty calculation does not create problems, especially compared to variance in reliability of data itself,  c) transaction costs (having to sign legal contracts, do paperwork, fill orders for multiple small transactions etc.) d) the subtleties of complying with international regulations can make people and businesses be unwanted as clients, based on their country of origin, due to censorship reasons etc. 

An additional reason for Verifier's existence is having network's own protection against cheating, rather than outsource it to Client's agents who could be corrupted, or regulated into censorship, or having an incentive to act with malice against the network.


Stage 6: Payment settling
---

1. If the Client has voluntarily released the payment
   - If W and V were in agreement, then W and V get the pre-agreed payments for their contribution to the Work.
   - If W and V were not in agreement, then the following distribution happens, unless canceled by V within a certain time limit. By this distribution, W gets the pre-agreed payment for his contribution to the Work, and V loses a significant part of the stake (around 75% would be an economically logical area that averts cheating, and still leaves enough incentives to V not to go to Arbitration without good reason). This penalty money is mostly burnt; a small part of money might be awarded to the Client for his contribution to network safety and/or for costs to have to re-check the Sample results because of W, V disagreement. If V cancels the distribution within the given time limit, then Arbitration. 
   
Explanation: If the Client has voluntarily released the payment, this means that he desires to receive the results and is ready to pay for them. This also means that the Client agrees with the Worker, not with the Verifier. Hence the process starts by offering to Verifier to surrender most of its stake.
   
2. If the Client selects that he will not voluntarily release the Payment, i.e. asks to back off (on the grounds that he is not satisfied with the Sample result quality, then the following distribution happens, unless canceled by W within a certain time limit. By this distribution, V gets the pre-agreed payment for his contribution to the Work, and W loses a significant part of the stake (around 75% would be an economically logical area that averts cheating, and still leaves enough incentives to W not to go to Arbitration without good reason). This penalty money is mostly burnt; a small part of money might be awarded to the Client for wasting time. If W cancels the distribution within the given time limit, then Arbitration.

Explanation: If the Client does not release the payment voluntarily, this means that he is not desiring to get the results. This also means that the Client agrees with the Verifier, not with the Worker. This is why the process starts with the offer to Worker to surrender most of its stake.

3. If the Client fails to reply about his choice within the given amount of time: 
   - If W and V were in agreement, then W and V get the pre-agreed payments for their contribution to the Work.
   - If W and V were not in agreement, then Arbitration. It may be reasonable (if not too difficult techically) to give them the chance to avoid Arbitration, by agreeing collectively to either of the two distributions: (W paid, V penalized 75%), (W penalized 75%, V paid).

There arises a question whether we can omit the choice of the Client not to release the payment voluntarily when W, V agree, if the Verifier was to be selected by the Client, on Client's full responsibility. That way the Client effectively loses the right to appeal to Arbitration, and his interests are represented by Verifier only. However, it might still be beneficial to keep separate the right of agents to appeal to Arbitration, so that the faulty Verifier that conspires with the Worker can be punished.

Stage 7: Arbitration
---

Arbitration, with as many (random) Arbiters as possible. To become eligible, Arbiters would need to put their own stake as collateral (comparable in size to stakes of W, V), so as to judge responsibly, having skin in the game.

One option is that Arbiters decide the outcome of Arbitration by simple majority, in that case they should be an odd number. Another alternative option is to design incentives for Arbiters, to discourage corruption, making it unprofitable. For example, if 70% of Arbiters agree on any outcome, then they are rewarded and those Arbiters that do not agree are seriously punished by stake cut. If less than 70% Arbiters agree on any outcome (i.e. the agreement rate is between 50% and 70%), then the totality of Arbiters is slightly punished and this money is used to re-do the Work anew or to refund the Client (while W, V get their stakes back untouched).

The limitation to the number of Arbiters is the collateral stake size that pays for Arbiters calculations; the collateral stake of the faulty W or V are distributed among Arbiters. The payment of Arbiters must not be different, whether they exonerate W or V. This is achieved by either having symmetric stakes w=v (right from start or when going to Arbitration), or by fixing Arbiters total payment as being the smallest from v, w. The Client's locked Payment never goes to the Arbiters, but is either refunded to the Client (when V is found true) or awarded to the Worker (when W is found true). 

Also, it is worth noting that when Arbitration starts, most previous choices of C, W, V become irrelevant, e.g. who initiated the Arbitration and on what grounds is irrelevant; the Arbitration is a greenfield approach.
