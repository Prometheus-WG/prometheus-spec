Smart contract
===

Payment settlement schemes
---

There are 8 possible combinations of the Worker, Verifier and Client node signatures on the unlocking transaction:(`2^3=8`), out of which 3 are nonsense/invalid (since they include either Client of Verifier signature, which does not make economic sense: there is no reason to pay if the actual computations were not made). Thus, the unlocking script of the Prometheus-type smart contract defines 5 scenarios with corresponding rewards:

Scenario               | Signatures | W txout | V txout | C txout | Script branch
---------------------- |:----------:|:-------:|:-------:|:-------:| -----------
Worker unresponsive    | none       |         |         |         | CLTV return of funds to `C`
Everything correct     | W+V+C      | s+w     | s/z+v   | 0       | 3/3 multisig
Client declined to pay | W+V        | w+x%    | v       | s-x%    | 2/2 multisig
Faulty Verifier        | W+C        | s+w     | 0       | 0       | 2/2 multisig
Faulty Worker          | W          | 0       | v+w+s/z | 0       | 1/1 multisig

Contract script
---

Let:
- `W` be a signature of a worker with stake `w`, 
- `V` be a signature of a verifier with stake `v`, 
- `C` be a signature of client,
- `s` the agreed payment from the client for a successful contract execution,
- `t` be an agreed maximum time for the contract execution.

All the stakes (`w+v`) and client payment (`s`) are deposed as tree *txins* with a single *txout*, locked with P2SH script.

Transaction script for hashing into `ScriptPubKey`:
```
-- Branch for 3/3 multisig: everything is correct
OP_DEPTH
<3>
OP_VERIFY
OP_IF
    OP_DROP2
    3 <W> <V> <C> 3
    OP_CHECKMULTISIG

-- Ok, something went wrong:
OP_ELSE

-- Branches for 2/2 multisigs
    OP_DROP
    <2>
    OP_VERIFY
    OP_IF
        OP_DROP2
        
-- "Client declined to pay" case (W+V multisig)
        2 <W> <V> 2
        OP_CHECKMULTISIG
        
-- "Faulty Verifier" case (W+C multisig)
        OP_NOTIF
            2 <W> <S> 2
            OP_CHECKMULTISIG
        OP_END

-- Branch for "Faulty Worker" case: 1-of-1 Worker multisig
    OP_ELSE
        OP_DROP2
        1 <W> 1
        OP_CHECKMULTISIG
        
-- Branch for "Worker unresponsive" case: CLTV return of funds to `C`
        OP_NOTIF
            1 <C> 1
            OP_CHECKMULTISIG
            <t>
            OP_CHECKLOCKTIMEVERIFY
        OP_END
    OP_END    
OP_END
```
