```
SIP: ?
Title: Storj Bridge Directory with an Ethereum Contract
Author: Braydon Fuller <braydon@storj.io>
Status: Draft
Type: Standard
Created: 2017-08-03
```

Abstract
--------
This document details a proposal for a decentralized directory, with an Ethereum contract, of Storj Bridges to improve the reliability and security of the network.

Motivation
----------

Currently there is a hardcoded value for a "trusted key" for Storj farmers in [storjshare](https://github.com/storj/storjshare-daemon), this creates friction with setting up new Storj Bridges, as it is then necessary to have Storj farmers manually add an additional "trusted key" to the configuration. By creating a decentralized directory of Storj Bridges with an Ethereum contract, a Storj Farmer could discover and automatically establish connection to receive storage contracts.

Specification
-------------

The initial setup of a Storj Bridge will include creating an Ethereum transaction to run the `add` method on the Storj Directory Ethereum contract:

```
struct Bridge {
    string name;
    address owner;
    string extended_key;
    string name_server;
    ...
}
```

The transaction will require that the owner address has a minimum STORJ balance, as to demonstrate the capability to pay farmers in STORJ tokens.

TODO

Reference Implementation
------------------------

TODO

Citations
--------------
1. https://github.com/Storj/sips/blob/master/sip-0002.md
2. https://github.com/paritytech/contracts/blob/master/TokenReg.sol
3. https://github.com/storj/storjshare-daemon