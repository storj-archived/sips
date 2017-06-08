```
SIP: ?
Title: Farmer Load Balancing Based on Reputation
Author: Braydon Fuller <braydon@storj.io>
Moby von Briesen <moby@storj.io>
Status: Draft
Type: Standard
Created: 2017-06-08
```

Abstract
--------
This document details a proposal to change how to select nodes to store data. The new method of publishing storage contracts takes advantage of nodes' reliability and geographic location while simultaneously ensuring that every node gets a chance to store files for the network.

Motivation
----------
- There is currently a lot of congestion on the Storj network caused by communication between renters and farmers with respect to publishing storage offers. This communication can be simplified.
- Farmers are not selected to store shards based on reputation at the moment. A farmer that is less reliable currently has (roughly) an equal chance of being selected to store a shard as a farmer that is more reliable.
- Farmers are not selected to store shards based on geolocation at the moment. By implementing geolocation, file upload and download speeds could be noticeably improved for individual users. In addition, geolocation implementation would incentivise new farmers in areas where, for instance, there are a lot of users uploading files to the storage network, but where there are not a lot of farmers.
- Storj farmers are not currently load balanced. One farmer that is just as reputable as another does not necessarily have an equal chance of receiving a storage offer, since offers are propagated based on the contacts of a particular renter, and the contacts of that renter's contacts (this is related to the first point)

Specification
-------------

There are two groups of nodes for the purpose of selecting a node to store data:

1. Active Nodes - Nodes that are considered fast and reliable
2. Benchmarking Nodes - Nodes that are going through a benchmarking phase

Publication of contracts will be sent to each of these groups in different proportions. For example, 75% of the contracts could be sent to active nodes and 25% to benchmarking nodes. This provides an opportunity for nodes with poor metrics to improve, while nodes with better metrics would receive more contracts for a more stable network. To make sure that there is equal distribution of requests, nodes will be selected based on the last time a contract was published to them, favoring the least recently used nodes.

### Active vs Benchmarking Nodes

There is one metric that can be used to divide an active vs. a benchmarking node. This implementation and metric can change over time and could be based on:
- A node's estimated moving average response time (currently available)
- A reputation field based on several metrics

The exact threshold used to divide the two groups can use a constant `BENCHMARK_THRESHOLD' that is updated periodically to represent the 25th percentile of all of the known contacts based on the metrics above. This way, the best 75% of nodes are considered active and the remainder are considered benchmarking. This is to keep the two pools of nodes proportional to the number of contract publications to the them, as described earlier.

#### Response Time

```
```

#### Reputation

The following equations could be used to calculate such a reputation:

For fields `i = 1...N`

If higher = better reputation:
```
boundedFieldI = (fieldI - LOWER_BOUND_I)/(UPPER_BOUND_I - LOWER_BOUND_I)
```
If lower = better reputation:
```
boundedFieldI = 1 - (fieldI - LOWER_BOUND_I)/(UPPER_BOUND_I - LOWER_BOUND_I)
```
```
0 <= FIELD_I_WEIGHT <= 1
FIELD_1_WEIGHT + ... + FIELD_N_WEIGHT = 1
reputation = FIELD_1_WEIGHT * boundedField1 + ... + FIELD_N_WEIGHT * boundedFieldN
```

For instance, if a contact's reputation was based on `responseTime` and `timeoutRate`, reputation might look like this:
```
boundedResponseTime = 1 - responseTime / 10000
boundedTimeoutRate = 1 - timeoutRate
reputation = 0.75 * boundedResponseTime + 0.25 * boundedTimeoutRate
```

### Future Improvements

There can be other enhancements to node selection to give bias to some nodes over others to narrow the active and benchmarking pools.

#### Geolocation

One flaw with calculating reputation based on response time or similar metrics will give bias to nodes geographically closer to Storj bridge services, especially prevalent when clients are also further away from the bridge.

There are services that allow for converting IP addresses to approximate location (latitude/longitude) [1]. In addition, MongoDB has support for geospatial indexes and queries [2]. Using these services, the database queries described above can be refined to prioritize farmers that are geographically closer to the user uploading a file.

This benefits users, as upload and download speed will be better for geographically closer farmers. It also benefits farmers, since they will be competing on a more local scale for reputation. In addition, the existence of regions that are abundant in users but scarce in farmers will provide incentives for farmers to pop up in those regions.


Reference Implementation
------------------------
https://github.com/Storj/bridge/pull/464


Citations
--------------
- https://www.maxmind.com/en/geoip2-databases
- https://docs.mongodb.com/manual/applications/geospatial-indexes/
- https://medium.com/@storjproject/how-to-ddos-yourself-dbcdc3625bd0
