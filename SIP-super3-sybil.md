```
SIP: XXXX
Title: Bounding Sybil Attacks with Identity Cost
Author: Shawn Wilkinson <shawn@storj.io>
Status: Draft
Type: Standard
Created: 2016-06-27
```

## Motivation
Any malicious farmer can spin up a large number of virtual farmers in order to get more data contracts. This attack becomes more difficult as the network becomes larger, but still can mean that the malicious farmer gets an unbalanced amount of contracts for her size/performance. This is known as a [Sybil attack](https://en.wikipedia.org/wiki/Sybil_attack).

This attack is easy to execute because virtual farmers are free to create and the attacker is only bounded by very cheap temporary computational resources. The solution is to introduce an identity cost. This cost should be low for the average farmer, but expensive for an attacker who would like to spin up many temporary virtual farmers.

It is important that renters not establish contracts based on this identity cost alone. Ideally, they should establish contracts based on established performance, and its relationship with those farmers. Therefore, renters may choose to give a contract to a farmer with no identity cost, but they should de-prioritize that farmer over farmers with an identity cost.

##### Math
A successful Sybil attack can be modeled with a [hypergeometric distribution](https://www.geneprof.org/GeneProf/tools/hypergeometric.jsp).

- Population size (P) - Number of farmers in the network
- Number of Successes in Population (p) - Number of malicious farmers in the network controlled by a signal entity.
- Sample Size (S) - Number of shards on the network per file.
- Number of Successes in Sample (s) - Number of shards that must disappear for the file to fail.


For example, with 10,000 farmers on the network and 1,000 attackers, if we Reed-Solomon erasure code 20-of-40, the probability of a successful attack on a file is 1.67e-11.

## Specification
1. A farmer deposits at least 0.0001 SJCX to her public node-id address once a month. The farmer may optionally deposit more SJCX for increased visibility. The farmer will also have to pay a Bitcoin transaction fee as Counterparty transactions rely on Bitcoin transactions.
2. Renters will evaluate these node ids when negotiating contracts, and give them higher priority over node ids that do not follow this process.

## Rationale
Using the [math](#math) provided above the network would yield 16 file failures per 1 trillion objects. To put that into perspective, Amazon had about 1,000 failures [1] and stored two trillion total objects in Q2 2013 [2]. To get a failure, the attacker would have to maintain millions of dollars in storage hardware and/or own a large percentage of the network.

Let us instead assume the attacker would like to appear as a very large portion of the network for a short amount of time. For example, if the number of attackers was increased to 5,000 out of 10,000 farmer,s our failure rate would be a very high 43%. It is worth noting that this doesn't actually affect renters with existing files on the network, because they have already found safe homes for their files before the attack was started. Choosing farmers is not a blind task, so we can even prevent the attack by:

 - Storing a majority of the data with farmers that we already have an existing relationship with.
 - Not storing data with farmers we/anyone else has never seen on the network before today.

Therefore, this attack only affects very new renters to the network, and those who are not being smart about their farmer selection. This attack is unbounded and only costs about $32.50/hour on Amazon EC2, at a current cost of $0.0065 per attack host/hour (EC2's smallest instance).

##### Bounding the Attack with Identity Cost
We want to help renters destinguish between good farmers and attackers. Current transaction fees are around $0.05, which means that the malicious farmer would have to spend $250/month to maintain the attackers, which is significantly more expensive than what it would be to run the attack for a very short period of time (1-3 hours). What this allows the renters to do is look at this information over time to establish trust. So a renter would never store more than 10% of her shards on hosts that have been online for less than a month, effectively reducing the attacks from 5,000 to 500, well within acceptable ranges.

A more determined attacker might build up these identities over time and then use them to execute an attack. Unfortunately for the attacker, she would have to maintain these identities at great cost to avoid being blacklisted by the network for poor performance before she could get an impactful amount of data.  


## Citations
```
[1] https://aws.amazon.com/s3/faqs/
[2] http://www.statista.com/statistics/222309/total-number-of-objects-stored-in-amazons-s3/
[3] https://aws.amazon.com/ec2/pricing/
```
