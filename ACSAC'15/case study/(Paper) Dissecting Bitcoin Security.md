
# Dissecting Bitcoin Security

By Cassio Goldschmidt

December 9th, 2015

## Bitcoin overview

Bob wants to send some money to Alice.
Alice sends its address to Bob, that then creates a "transaction". 
The transaction will be checked by the community, and finally validated.

### What's the big deal with bitcoin?

* Decentralized, censorship resistant system
* Permission less
* Public transactions
* Immutable record
* Trustless (reduced trust) validation
* Interoperable protocol
* Programmable money (for BTC)

All of those characteristics => let's think about security.

###  Bitcoin addresses

* Hash of a hash of a ECDSA public key
    * public key is protected
    * key space is huge
    * no central authority
* Base 58 encoded + "checksum" + prefix
    * avoid typos

Chances of having an address collision are really really small.

## Pay-to-Script-Hash (P2SH) addresses

Hash of a script used as an address
* very limited functionality
* no infinite loops or logic bombs

One of two signatures or two of three signatures.

Because addresses use private and public keys, you don't have the same problems
that you have with credit cards (for which you need to give your account number for instance). 
Because you give an address for one particular transaction, it cannot be reused.

### P2SH Example

Bob pays Alice 10 BTC. Bob pays Alice exactly like he did before. 

## Bitcoin wallets

### Types and functions

* __Client side wallets__: application that runs in your PC, you manage and secure keys yourself
* __Web wallets__: stored on the web and protected by a third party
* __Cold wallets__: keys are stored offline in paper or USB keys
* __Hardware wallets__: mini HSM that provides encryption, authentication and anti-tampering features
* __Brain wallets__: computer generates a passphrase and the user memorize it

__A wallet does not contain coins, it just contains keys!__

### Implementation

* __Non-deterministic (random) wallets__: just a bunch of keys - need to backup keys frequently
* __Deterministic seeded wallets__: seed + index or chain code is used to derive the private key - all keys can be recovered from the seed
* __Hierarchical deterministic wallets__: parent key can derive a sequence of children keys - branches ca be used to only receive or to only spend funds - users can create public keys without having access to private keys

## How payments work?

* One or more inputs: unspent transactions, public key(s), signature(s)
* One or more other outputs: addresses to pay, BTC
* Timestamp: `<time, date>`

### A peek inside the cloud

Blockchain is a chain of blocks where a block is a transaction.

Job of miners:
* Validate new transactions
* Record the work in the blockchain
* Verify the work of other miners -- proof of work
* Reward fees

#### Block header

```
Version info
-- 
Nonce
--
Previous block hash
--
Timestamp
--
Merkle tree hash
--
Transaction id list
```

The block, to be created, need to create a hash given some restrictions given by the nonce.

### Proof of work is like a game of sudoku
* A hard problem to solve
* But very easy to verify
* Keeps the generation of new bitcoins constant!

Bitcoin proof of work:

*Repeatedly hash the header of the block and a random number until the hash has a certain number of leading zeros.*

## Alternative coins

* Namecoin: decentralized key-value registration and transfer platform using a blockchain. Used for as an alternative DNS.
* Bitmessage
* Augur
* MIT Enigma
* Notary Services
* Ethereum Frontier
