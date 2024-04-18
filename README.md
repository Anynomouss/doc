[WARNING THIS IS WORK IN PROGRESS AND MIGHT CONTAIN WRONG INFORMATION]  
# Grin from a User perspective
This documentation aims to explain Grin and mimblewimble from a user viewpoint.
The outline of the document follows a 'User Story' approach to explain the "magic" of mimblewimble. This document serves as an Add-on that you might want to read after looking at  the [official documentation](https://docs.grin.mw/wiki/table-of-contents/https://docs.grin.mw/wiki/table-of-contents/). If you prefer a more conceptual high level description, I highly recommend reading [this explanationon by Phyro](https://phyro.github.io/what-is-grin/). Where possible I simplify while at the same time being **explicit about the information flow between [users] - [nodes] - [wallets]**. 
This documentation is self serving, and represents my own struggles as a muggle, visual thinker, programmer and aspiring wizard, to understand Grin. Since I have a background in Bitcoin, I will make many comparisons with Bitcoin; highlighting where Grin is different from Bitcoin. 

**Outline of this document:**

 1) How does my Grin wallet work?
 2) how does my wallet create a transaction?
 3) How does my transaction  end up on chain?
 4) How does my wallet knows which outputs belong to it? 
 
If this explanation does not work well for you, scroll down to references. Reading about Grin from multiple angles helped me greatly on my journey to understand Grin. If you get lost in any section, simply skip forward to the "TAKE AWAY" section that summarizes it.
***
# How does my Grin wallet work?
Grin wallets are generated the same way as most Crypto wallets. Grin follows the BIP32 standard in deriving a master seed from a mnemonic seed phrase (BIP39) and deriving children keys as a [Hierarchically Deterministic (HD)](https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/) wallet. The process for deriving BIP32 HD wallets is that you start with a randomly generated number called a (**seed**), which is presented to the users as a list of words called the **mnemonic seed phrase** (12-24 words). From this seed phrase a master key as well as children keys are generated. HD wallets can be best understood by visualizing them as a tree:

    BIP32:
    Master  / Account/ Purpose / index
   
                          / --3  (spend)
                         / ---2  (spend)
                        0 ----1  (spend)
                       /    
    Master ---- 0 ---- |
                       \    
                        1 ---- 1 (change)
                         \ --- 2 (change)
                          \ -- 3 (change)
                
 

  
The most important thing to understand about your Grin wallet is that all keys are Hierarchically Deterministic (HD). This means that as long as you have the **seed(phrase)**, you can restore your wallet and derive the complete tree of derived keys. Losing funds after a recovery from the seed phrase is impossible! 


**Does my Grin wallet differ in any my Bitcoin wallet?**  
There are two important differences when using your Grin wallet compared to Bitcoin. These differences have to do with how Grin transaction data is stored. Grin nodes can freely "forget" transaction data from outputs that are no longer relevant, such as spend UTXO's. This is great for keeping the blockchain lightweight by pruning unneeded data, but it also means your wallet cannot recover information about spend transactions from the blockchain. Secondly, Grin is amazing for privacy since all outputs in a transaction can be aggregated in 1)  Dandelion phase 2) in a block, and 3) get aggregated by your node. This means that when you restore your wallet, even your own wallet cannot retrieve the link between inputs and outputs in a block. Your wallet can only scan the blockchain to see which outputs are yours. Therefore, your wallet balance is always correct, even if your wallet does not know your full transaction history.

Secondly, Grin does not store all transaction information on chain. Payment proofs are created interactively between you and the party you interact. Not storing Payment proofs on-chain keeps Grin lightweight and privacy preserving. The downside is that your wallet will not be able to restore payment proofs when recovering from the seed phrase. So as merchant who would use Grin, it would be wise to make the occasional backup of your *wallet_data* folder.


> **WARNINGS:**  
*1) Grin wallets store the master key without using hardened derivation. This means that the private-key can be converted back to the mnemonic seed phrase and should not be reused for other wallets.   
2) As Merchant or Exchange, it would be wise to make the occasional backup of your **wallet_data** folder to have a backup of both spend transactions and a backup of payment proofs.*  


***
# How are transactions in Grin different from Bitcoin?
**Bitcoin transactions:**
In Bitcoin, the sender is in full control and signs all transaction data by signing the transaction data with a [digital signature](https://learnmeabitcoin.com/beginners/guide/digital-signatures/https://learnmeabitcoin.com/beginners/guide/digital-signatures/). Bitcoin addresses are either hashed public keys (legacy address) or a hash of a lock-script that includes your public key [[REF](https://learnmeabitcoin.com/technical/keys/address/https://learnmeabitcoin.com/technical/keys/address/)]. Whenever someone sends Bitcoin to your address, he transfers ownership of the Unspent Transaction Outputs (UTXO's) in that transaction by saying *"Anyone who can provide the lock-script together with a valid signature for this address, can spend all outputs associated to it"*. Since only you can provide a signature for your public key/address, only you can spend your coins. All miners can verify that your signature matches the lock script, after which they will include your transaction in the next block to be mined. 

**Grin transactions:**
Grin transactions have two big differences from Bitcoin transactions. First, in Grin transactions, both **sender and receiver are in full control of their own inputs and outputs**. To receive or spend an output, you have to 'Sign' for that output using your wallet. This means that to send a transaction you have to interact with the receiver since both your and the receivers wallet need to provide information for your own inputs and outputs ([See this explanation](https://phyro.github.io/what-is-grin/interactive_txs.htmlhttps://phyro.github.io/what-is-grin/interactive_txs.html)). Secondly, in a Grin transaction, **sender and receiver jointly create a Signature** for the transaction. In the next section I will attempt to explain to achieve some very cool properties such as a) hiding amounts, b) not needing addresses, c) making transactions easy to aggregate

Grin transactions involve three cool tricks:    
1) **Homomorphic Commitments**: In Grin, values are hidden by multiplying all values with a Generator point. Although the result of multiplying a value with a generator point results in a seemingly random number. Addition and subtraction still work. This means that when add up numbers multiplied with a generator point on the Elliptic Curve, they still add up. E.g. if a transaction would involve two input and one output value `v1+v2=v3`, they can be multiplied with generator point `H` on an Elliptic Curve and anyone can verify that `v1*H + v2*H = v3*H` without knowing what `v1`, `v2` or `v3` is. 
2) **Pedersen Commitments**: As explained above, Grin transactions hide the amount in an output by multiplying them with a generator point. However, this would allow people to find `v1`, `v2` or `v3` by guessing them. To solve this problem, a second trick is applied by adding a private-key multiplied with another generator point `G` to the output commitment. This effectively makes it impossible to ques the value in an output:<br/><br/>  
`Output = K1*G+v*H`.<br/><br/> 

3) **Joint Schnor signatures:** Hold on, if we add keys `K1*G` and `K2*G` to the output commitments, the outputs will not add up to zero right? The excess value of a transaction is the sum of all outputs blinding factors, minus the sum of all private-keys used to blind the inputs and outputs of a transaction.
 
Indeed, by adding all private-keys times generator point G, we create an `excess`. By proving we know this excess (with is just another point on the curve), we prove we know `K1` and `K2`.
To an outsider the output commitment looks like a random piece of data : `09551fd2ba097bbf53d027c9820c2f19a544a15f21cb46614ce5860077a3663181`.<br/><br/>  
 Now lets say make a transaction with one input, one output for the receiver and one change output (we will ignore fees):<br/><br/>
`C1 = K1*G + v1*H`  
`C2 = K2*G + v2*H`
`C3 = K3*G + v2*G` <br/><br/>
How can you prove to an outsider you actually know the private keys `K1` and `K2` and that the receiving party knows `K3`? Remember that addition using EC generator points still works, meaning that we can combine these two three commitments `C1`, `C2` and `C3` in a new commitment `Z` by adding them. Also remember that all value add up to zero even when multiplied by `H`! 

The point `Z` (remember a commitment is simply a point on the curve) is the result of addition between points C1 and C2.<br/><br/>
`Z = C1 + C2 + C3`  
`Y - Xi = (K2+K3-K1)*G +(v2+v3-v1)*H = excess*G + 0*H`  
Where `Y - Xi` is a valid public key for generator point G; which is the case only if `Y - Xi = r*G + 0*H`. In other words, if the values don't sum to 0, the result is recognized as an invalid public key for G. This ensures that:

 *  The transacting parties can collectively produce the excess value (it is the private key of their **joint signature**).
 * **The sum of the outputs minus the inputs is 0**, because only a valid public key for G will check out against signature they generated.
<br/><br/>

This is the foundation for the Elliptic-curve algebra used in Mimblewimble to **prove both ownership of outputs (coins) and non-inflation****.

The excess value of a transaction is the sum of all outputs blinding factors, minus the sum of all inputs blinding factors, `ro - ri`.


> **TAKE AWAY:**    
Grin transactions are interactive, each party signs for their own outputs and jointly they generate a signature to prove that a) all values add up to zero, and b) that they know all private-keys by creating a signature for the private-key excess. Grin ***output commitment*** are both ***binding*** and ***hiding***. Any node can verify the outputs in a transaction, in a block or in the entire block-chain without knowing any of the output values. All outputs are Homomorphic and can therefore freely be aggregated as a) transaction  b) as block or c) as blockchain. A node treats the Grin blockchain as one large transaction and verifies:   
`Σ utxo = Σ kernel + height * 60 * H`   
to prove that no new coins are created apart from the block reward. This trick of "forgetting" spend outputs is called *[cut-through](https://docs.grin.mw/wiki/introduction/(og)introduction-to-mimblewimble/#cut-throughhttps://docs.grin.mw/wiki/introduction/(og)introduction-to-mimblewimble/#cut-through)*.
*** 


# How does my transaction  end up on chain?
Grin transaction are broadcasted to the peers your nodes connects to. Since the node does use the Tor network, it means the nodes you connect to knows that you send your transaction as well as your IP address. To protect you from this privacy leak, *grin-wallet* by default uses Dandelion to help protect your anonymity [REF] [https://docs.grin.mw/wiki/miscellaneous/dandelion/].
Remember that Grin transactions can be freely aggregated. Since there are no addresses or otherwise identifying information in transactions, aggregating transactions makes chain-analysis for Grin very uninformative. The more transactions are aggregated, the hard it will be to know which inputs and outputs are linked. Simply put, unless an observer had access to this data before aggregating, he or she does not know which inputs or outputs are involved in the same transaction. That is pretty neat trick to improve the anonymity for all transactions.  
If you doubt this yourself, try restoring your wallet from the seed, you soon will find out that even your own wallet cannot remember outputs that were spend nor will your wallet be able to deduce from the blockchain who send you an output.

>**TAKE AWAY*:  
First, grin transaction data consists of ***inputs***,  ***outputs*** and a ***transaction kernel*** and a ***range-proof**. 
Second, transaction data can be aggregated [coinJoin, Dandelion, block, entire chain] at any point or time. This effectively means the grin blockchain can be considered one entire big transaction.   
Third, spend outputs can be freely forgotten thanks through *cut-through* in any of the aggregation steps. This **greatly improves anonymity and Scalability.** Add to this the fact that Grin outputs are blinded and you get an incredibly, lightweight, scaleble, and privacy preserving blockchain!
***

# How does my wallet knows which outputs belong to it?  
Output commitments themselves cannot be decomposed by your wallet. Output commitments are binding and hiding and provide **proof** of **ownership** and **non-inflation**. The real information about outputs is retrieved from their associate**range-proof**. Range-proofs are XOR'ed with a *nonce* and *index* to indicate which wallet key were used. Since your wallet can scan for range-proof that are  XORed with your wallet keys, it scan for outputs and decode the *amount* and *key index* for from the range-proof. The wallet now knows the *amount* and *key index* it retrieved from the range-proof of outputs the wallet wants to use. The wallet can use these to calculate new outputs and the transaction kernel excess jointly with the receiver. The jointly calculated excess of private keys is called the **transaction kernel** and only is a valid valid public key for G if all values in the transactions add up to zero. Hence the kernel excess is both a signature to prove ownership of the keys as well as proving no new coins are create through the transaction (non-inflation)!.  [[REF](https://tlu.tarilabs.com/cryptography/bulletproofs-and-mimblewimblehttps://tlu.tarilabs.com/cryptography/bulletproofs-and-mimblewimble)]

# Mind boggling realization :exploding_head: 
* Mimblewimble is a new blockchain format
* Grin nodes aggregate all blocks and treats it as a single transaction
  * Proving non-inflation: `Σ utxo = Σ kernel + height * 60 * H`. 
  * *Meaning the total supply is height* 60*
* Grin transactions consist purely of commitments and range-proofs without public data.
    * Outputs that are spend are forgotten, meaning their range-proof is no longer stored only leaving the transaction kernel [inputs +  outputs + kernel excess ]
* Transactions are 




***
# References
https://github.com/mimblewimble/docs
https://docs.grin.mw/wiki/introduction/mimblewimble/mimblewimble/
https://docs.grin.mw/wiki/table-of-contents/
https://phyro.github.io/what-is-grin/interactive_txs.html
https://phyro.github.io/what-is-grin/mimblewimble.html
https://medium.com/@brandonarvanaghi/grin-transactions-explained-step-by-step-fdceb905a853
https://tlu.tarilabs.com/protocols/mimblewimble-transactions-explained
https://tlu.tarilabs.com/cryptography/bulletproofs-and-mimblewimble
