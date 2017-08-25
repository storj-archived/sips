```
SIP: ?
Title: Farmer Time-locked Storage Payouts
Author: Braydon Fuller <braydon@storj.io>
Status: Draft
Type: Standard
Created: 2017-08-14
```

Abstract
--------
This document details a proposal for storage payouts by using Ethereum smart contracts to time-lock STORJ tokens that can later be claimed with probabilistic storage proofs. Tokens that are unclaimed can then trigger an event to further replicate or recover data with erasure encoding.

Motivation
----------

- There are scalability issues storing data is decentralized block chains, space is very limited so it's essential to compress meaning as much as possible.
- Currently "proof of retrievability" as described in [Storj whitepaper](https://storj.io/storj.pdf) [1] uses challenges for audits. Renewing and extending contracts would create an issue where challenges need to be regenerated, this does not scale as the entire file will need to be downloaded and hashed again. If a network stores a zettabyte, that amount of data would need to be transfered and hashed periodically.
- Storage payouts is currently a manual process that is run once a month.
- While it's expected that bandwidth will be a primary incentive for farmers to retain data, this could put emphasis on data that is retrieved often and repeatedly. There should be additional incentives to store data.
- A farmer "ping" event does not verify that a farmer has a file, only that the farmer is online.
- The ability to monitor farmer uptime will degrade if the service isn't running 24 hours a day. A monitor should be able to have tolerable limits to be offline and can utilize the [Ethereum blockchain](https://www.ethereum.org/) [2] for the purpose.

Specification
-------------

An Ethereum smart contract is used to lock up STORJ tokens that can later be claimed by farmers by proving probabilistic storage of expected shards. A similar concept of storage proofs has been described in the [Sia whitepaper](http://sia.tech/sia.pdf) [3]. The emphasis in this specification is on monitoring farmer uptime via proofs of large sets of shards. The [Chainpoint whitepaper](https://tierion.com/chainpoint) [4] describes a similar scalable means of anchoring data in a block chain with merkle trees.

### Farmer State Tree

A [merkle tree](https://en.wikipedia.org/wiki/Merkle_tree) [5] is generated from a snapshot of shard roots that the farmer has been scheduled to store data within a set duration, for example a week time period. This tree is then shared between a farmer and bridge. The merkle root of those shard hashes is added to an Ethereum smart contract that will time-lock STORJ tokens that will be released by proving storage of random segment within a random shard.


```
             farmer-root
               +     +
            +            +
         +                   +
      hash                   hash
      +   +                 +    +
    +       +             +        +
  +           +         +            +
shard-0  |  shard-1  |  ...  |  shard-10001
```


### Shard Tree

Each shard will also have a merkle tree, performed on segments of the shard. The segment size can be optimized to create a tree at the optimal height as to minimize the size of the proof that will be necessary to release STORJ tokens on the smart contract.

### Smart Contract

The Ethereum contract will time-lock STORJ tokens and will release tokens with a proof at random intervals. For example if 24000 tokens are time-locked in the contract, and the contract is for 2 years, about every month 1000 tokens would be available to claim.

There will be a window after the period to claim the tokens, for example 1 week. To prove storage, a random segment in a random shard will be selected based on the block hash at the end of the claim period. The segment plus a list of hashes in the tree from segment all the way to the farmer root will need to be provided.

If any of the tokens are not consecutively claimed for a threshold number of times, all remaining STORJ tokens are returned to the owner. An event can be monitored when a shard proof is not claimed that can be warning that a shard either needs to be replicated or recovered from erasure encoding.

Reference Implementation
------------------------

TODO

Citations
--------------

1. Storj whitepaper v2 - https://storj.io/storj.pdf
2. Ethereum - https://www.ethereum.org/
3. Sia whitepaper - http://sia.tech/sia.pdf
4. Chainpoint whitepaper - https://tierion.com/chainpoint
5. Merkle tree - https://en.wikipedia.org/wiki/Merkle_tree
