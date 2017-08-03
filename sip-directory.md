```
SIP: ?
Title: Storj Bridge and Farmer Directory with an Ethereum Contract
Author: Braydon Fuller <braydon@storj.io>
Status: Draft
Type: Standard
Created: 2017-08-03
```

Abstract
--------
This document details a proposal for a decentralized directory, with an Ethereum contract, of Storj Bridges and Storj Farmers to improve the reliability and security of the network.

Motivation
----------

Currently there is a hardcoded value for a "trusted key" for Storj farmers (storjshare), this creates friction with setting up new Storj Bridges, as it is then necessary to have Storj farmers manually add an additional "trusted key" to the configuration. By creating a decentralized directory of Storj Bridges with an Ethereum contract, a Storj Farmer could discover and automatically establish connection to receive storage contracts.

Additionally, Storj Bridges need to discover farmers to store data. Storj Farmers can create many contacts to very cheaply to increase the chance of receiving more contracts, a solution was proposed in [SIP2](https://github.com/Storj/sips/blob/master/sip-0002.md) to bound Sybil attacks with identity cost. However, requiring STORJ token to receive STORJ token creates a causality dilemma. Using ETH to list a contact to receive STORJ tokens could escape the dilemma, as it's already necessary to have ETH to pay for gas for STORJ transactions.

Specification
-------------

The initial setup of a Storj Bridge will include creating an Ethereum transaction to run the `add` method on the Storj Directory contract, with the following details and type of "bridge", as similar a Storj Farmer will do the same with a type of "farmer":

```
struct Contact {
    string name;
    enum type;
    address owner;
    string id;
    string name_server;
    ...
}
```

TODO

Reference Implementation
------------------------

TODO

Citations
--------------
1. https://github.com/Storj/sips/blob/master/sip-0002.md
2. https://github.com/paritytech/contracts/blob/master/TokenReg.sol
