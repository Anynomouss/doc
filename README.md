[WARNING THIS IS WORK IN PROGRESS AND THEREFORE CONTANS INCOMPLETE AND INCORRECT INFORMATION]  
# grin-for-muggles
This documentation aims to provide a pragmatic user oriented description of Grin and mimblewimble, explaining the 'magic' for muggles and programmers like me. The objective is to be pragmatic and conceptual while at the same time being explicit and providing more practical information about the flow of information. 

This text describe a) how transactions are made, send, aggregated, stored, and b) how your wallet works, and how relevant information is retrieved from block-chain. 
The objective of this documentation is self serving, to help me as a conceptual thinker, programmer understand Grin and mimblewimble better. Since I have a background in Bitcoin, this explanation contains many comparisons with Bitcoin, highlighting where Grin is similar and where Grin different from Bitcoin. The way this explanation is order is different from most documentation out there since it provides a user story:
 1) how your Grin wallet and keys are generated 
 2) how your wallet interactively creates a transaction
 3) broadcast your transaction which gets aggregated
 4) how your wallet derives its state (how much you can spend) from the blockchain. 
 
If this explanation does not work well for you, scroll down to the many references of other explanations which describe Grin in more dept and from different angles:   
- The best beginning point for reading is the Grin and mimblewimble  [official documentation](https://docs.grin.mw/wiki/table-of-contents/https://docs.grin.mw/wiki/table-of-contents/).
- Another good and conceptual read, describing Grin as [different block-chain protocol](https://phyro.github.io/what-is-grin/mimblewimble.htmlhttps://phyro.github.io/what-is-grin/mimblewimble.html), comparing it with Bitcoin

# How is your grin wallet generated?
Grin wallets are essentially like any other Crypto wallet out there. Grin follows the BIP32 standard in deriving a master seed from a mnemonic seed phrase (BIP39) and deriving children keys as a Hierarchically Deterministic (HD) wallet. The process for deriving BIP32 HD wallets is that you start with a randomly generated number, which can be represented by a mnemonic seed phrase (12-24 words). For a more elaborate description of how HD wallets  work [see this link](https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/). In HD wallets, the random number generated from your mnemonic seed phrase is used to derive a master-key `/m`.
BIP32 wallets follow the derivation format `master/account/purpose/index`. E.g. as a single user, your account number would be 0 and your first derived address as well, meaning your first address index key would be `/m/0/0/0`, your second  `/m/0/0/1`, etc. For each output in a Grin transaction a new private-key is generated.

Warning: Grin wallets store the master key without using hardened derivation. This means that the private-key can be converted back to a mnemonic and should not be reused for other wallets. 
Grin wallets work exactly like Bitcoin and other crypto wallets when it comes to deriving your wallets Private Key - Public Key pairs. 
{Programmers: In the Grin code, the wallet keys are referred to as the keychain [continue writing here] }

# How are transactions in Grin different from Bitcoin?
In Bitcoin, the sender is in full control and signs all transaction data by creating a [digital signature](https://learnmeabitcoin.com/beginners/guide/digital-signatures/https://learnmeabitcoin.com/beginners/guide/digital-signatures/). Whenever someone send Bitcoin outputs to an address in your wallet, you can prove the address is yours by revealing your public-key since the wallet address is just a hash of your public-key. By signing a transaction using your private-key and by revealing your public key, miners can verify that 1) you own the output since miners can derive the address by hashing the shared public key and 2) that you are the owner of those outputs since you sign the transaction data with your public key. The miners and anyone else in the network can validate your address ownership and signature using the public key included in the transaction data.

In mimblewimble/grin transactions, both sender and receiver are in full control of their own inputs and outputs. This means that to send a transaction, the sender and receiver have to interact since they both need to provide their own information about inputs and outputs ([See this explanation](https://phyro.github.io/what-is-grin/interactive_txs.htmlhttps://phyro.github.io/what-is-grin/interactive_txs.html)).   
Although simplified, both Sender need to provide proof of ownership and non inflation (no new coins created) by 

To  better understand this, In order to sign/commit to an output, two pieces of information are needed. The owner needs to prove ownership


# How are transactions stored and shared by nodes?

# How are transactions scanned for - wallet state


# References
https://docs.grin.mw/wiki/table-of-contents/
https://github.com/mimblewimble/docs
https://github.com/mimblewimble/grin-wallet/tree/master/doc
https://github.com/mimblewimble/grin/tree/master/doc
https://tlu.tarilabs.com/protocols/mimblewimble-transactions-explained
https://phyro.github.io/what-is-grin/mimblewimble.html
https://gist.github.com/phyro/ec37d8bfedd36102b0ea5824580d06e4
https://phyro.github.io/what-is-grin/interactive_txs.html
https://phyro.github.io/what-is-grin/grin_emission.html

https://github.com/mimblewimble/grin-wallet/blob/75363a9a258bc1fb0cf60bfb4c88a8a653b122f2/libwallet/src/address.rs#L37
https://github.com/mimblewimble/grin-wallet/blob/75363a9a258bc1fb0cf60bfb4c88a8a653b122f2/api/src/owner.rs#L2032

## Questions regarding committing/signing a transaction
In Grin each party sign/commits their own input and output data, not the complete transaction.
So how are transactions signed in Grin? Is it the commitment, proof of 