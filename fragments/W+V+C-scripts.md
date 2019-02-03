
Let :
- `W` be a signature of a worker with stake `w`, 
- `V` be a signature of a verifier with stake `v`, 
- `C` be a signature of client,
- `s` the agreed payment from the client for a successful contract execution,
- `t` be an agreed maximum time for the contract execution.

All the stakes and client payment are deposed as tree *txins* of first transaction.

Transaction #1 script for hashing into `ScriptPubKey`:
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

