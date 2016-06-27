```
SIP: XXXX
Title: Bounding Sybil Attacks with Identity Cost
Author: Shawn Wilkinson <shawn@storj.io>
Status: Draft
Type: Standard
Created: 2016-06-27
```

## Motivation
Any malicious farmer can spin up a large amount virtual farmers in order to get more data contracts. This attack becomes more difficult as the network becomes larger, but still can mean that the malicious farmer gets an unbalanced amount of contracts for its size/performance. This is known as a [Sybil attack](https://en.wikipedia.org/wiki/Sybil_attack).

This attack is easy to execute because virtual farmers are free to create, the attacker is only bounded by very cheap temporary computational resources. The solution is to introduce an identity cost. This cost should be low for the average farmer, but expensive for an attacker would like to spin up many temporary virtual farmers.

It is important that renters not establish contract based on this identity cost alone. Ideally it should establish contracts based on established performance, and its relationship with those farmer. Therefore, a renter may choose to give a contract to a farmer with no identity cost, but it should de-prioritize that farmer over farmers with an identity cost.

##### Math
A successful Sybil attack can be modeled with a [hypergeometric distribution](https://www.geneprof.org/GeneProf/tools/hypergeometric.jsp).

- Population size (P) - Number of framers in the network
- Number of Successes in Population (p) - Number of malicious farmers in the network controlled by a signal entity.
- Sample Size (S) - Number of shards on the network per file.
- Number of Successes in Sample (s) - Number of shards that must disappear for the file to fail.


For example, with 10,000 farmers on the network, and 1,000 attackers, and using Reed-Solomon erasure code 20-of-40 the probability of a successful attack on a file 1.67e-11.

## Specification
1. Farmer deposits at least 0.0001 SJCX to their public node id address once a month. The farmer may optional deposit more for increased visibility. The farmer will also have to pay a Bitcoin transaction fee as Counterparty transaction rely on Bitcoin transactions.
2. Renters will evaluate these node ids when negotiating contracts, and give them higher priority over node ids that do not follow this process.

## Rationale
Using the [math](#math) provided above the network would yield 16 file failures per 1 trillion objects. To put that in perspective Amazon, would have about 1,000 failures [1] and stored 2 trillion total objects in Q2 2013 [2]. To get a failure the attacker would have to maintain millions in storage hardware and/or own a large percentage of the network.

Let us instead assume the attacker would like to appear as a very large portion of the network for a short amount of time. For example, if the number of attackers was increased to 5,000 of 10,000 farmers our failure rate per is a very high 43%. It is worth noting that this doesn't actually affect existing files on the network, because they have already found safe homes for the files before the attack was started. Choosing farmers is not a blind task, so we can even avoid the attack by:

 - Storing a majority of the data at farmers that we already have an existing relationship with
 - Not storing data with farmers we/anyone else has never seen on the network before today

Therefore this attack only affects very new renters to the network, and those are not being smart about their farmer selection. This attack is unbounded, and only costs about $32.50/hour on Amazon EC2, at a current cost $0.0065 per attack host/hour (EC2's smallest instance).

##### Bounding the Attack with Identity Cost
We want to help renters establish between good farmers and attackers. Current transaction fees around $0.05, which means the attacker would have to spend $250/ month to maintain the attackers, which this is significantly more than what it would be to run the attack for a very short period of time (1-3 hours). What is allows the renters to do is look at this information over time to establish trust. So a renter would never store more than 10% of its shards on hosts that have been online for more than a month, effectively reducing the attackers from 5,000 to 500, well within acceptable ranges.

A more determined attacker might build up these identities and then use them to execute an attack. Unfortunately for the attacker, they would have to maintain these identities at great cost to avoid being blacklisted by the network for poor performance before they could get an impactful amount of data.  


## Citations
[1]https://aws.amazon.com/s3/faqs/
[2]http://www.statista.com/statistics/222309/total-number-of-objects-stored-in-amazons-s3/
[3]https://aws.amazon.com/ec2/pricing/
