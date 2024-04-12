# grin-for-muggles
A pragmatic description of Grin and mimblewimble, explaining the 'magic' for muggles like me. The objective is to be pragmatic and conceptual in explaining how mimblewimble is implemented in Grin. How transactions are made, send, aggregated, stored, and retrieved by your wallet.
This description is therefore very self serving, to help myself as conceptual thinker. 
If this explanation does not for you, scroll down to the references. Many people described mimblewimble in various ways, and for each unique mind one way or a couple of them together might work best for you.

# How is your grin wallet generated?
Grin wallets are essentially a Hierarchically Deterministic generated wallet according to the BIP32 standard, just like most crypto wallets out there. The process is that you start with a random generated number, which can be represented by a mnemonic seed phrase. This random number is than used to hierarchically generate a bunch of private-keys. For each transaction, your wallet will derive a new private-key public key pair.  
Public and Privatekey pairs are generated using Eliptic Curve, in case of Grin

# How are


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