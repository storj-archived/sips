```
SIP: XXXX
Title: Bounding Sybil Attacks with Identity Cost
Author: Shawn Wilkinson <shawn@storj.io>
Status: Draft
Type: Standard
Created: 2016-06-24
```

## Motivation
Any malicious farmer can spin up a large amount virtual farmers in order to get more data contracts. This attack becomes more difficult as the network becomes larger, but still can mean that the malicious farmer gets an unbalanced amount of contracts for its size/performance. This is known as a [Sybil attack](https://en.wikipedia.org/wiki/Sybil_attack).

This attack is easy to execute because virtual farmers are free to create, the attacker is only bounded by very cheap temporary computational resources. The solution is to introduce an identity cost. This cost should be low for the average farmer, but expensive for an attacker would like to spin up many temporary virtual farmers.

It is important that renters not establish contract based on this identity cost alone. Ideally it should establish contracts based on established performance, and its relationship with those farmer. Therefore, a renter may choose to give a contract to a farmer with no identity cost, but it should de-prioritize that farmer over farmers with an identity cost.

##### Math
A successful Sybil attack can be modeled with a [hypergeometric distribution](https://www.geneprof.org/GeneProf/tools/hypergeometric.jsp). For example, with 10,000 farmers on the network, and 1,000 attackers, and using Reed-Solomon erasure code 20-of-40 the probability of a successful attack on a file 1.67e-11.

## Specification
1. Farmer deposits at least 0.0001 SJCX to their public node id address once a month. The farmer may deposit more for increased visibility.  The farmer will also have to pay a Bitcoin transaction fee as Counterparty transaction rely on Bitcoin transactions.
2. Renters will evaluate these node ids when negotiating contracts, and give them higher priority over node ids that do not follow this process.
