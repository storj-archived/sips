```
SIP: ?
Title: Bandwidth Quality Assurance
Author: Braydon Fuller <braydon@storj.io>
Status: Draft
Type: Standard
Created: 2017-09-13
```

Abstract
--------
This document details a proposal to more accurately account for bandwidth usage by a Bridge and to ensure that shards can be successfully transferred between Client and Farmer.

Motivation
----------
- A Bridge is currently unable to track if a shard transfer was successful and only that a request was made.
- Response time is currently used as a metric to prioritize which Farmers to store data. However there are issues with this because it doesn't account if shards have actually been transferred, only that it was possible to make a request. Successful transfer of shards between Client and Farmer should be considered.
- Clients can have too much bandwidth accounted as some of the shard transfers will have not been successful, each request is currently logged without consideration of failure.
- Farmer bandwidth related payouts can be incorrectly accounted. *Exchange Reports* can currently be used as confirmation from the Client, however missing reports are not considered. Sending payment without actual success could lead to misaligned incentives and decline of quality.

Specification
-------------

There are three perspectives for each shard transfer; the Client, Bridge and Farmer.
- A Bridge will record a *Storage Event* each time that a shard upload or download is requested.
- The Client will then send an *Exchange Report* on the SUCCESS or FAILURE of that transfer. Each report MUST match with a corresponding *Storage Event*, otherwise it should be ignored.
- A report from the Farmer can help confirm failure on the Client. However accurate reporting shouldn't be expected. As we can see by taking a look at the possible combinations and how the discrepancies can be resolved, and that the most relevant data is from the Client:

![Resolving discrepancies in perspectives](sip-bandwidth/exchange-reports.png)

A Client report of a FAILURE or otherwise UNKNOWN will be handled with special consideration for the purpose to encourage it's only used when there is actual FAILURE.
- There will be a limited percentage of FAILURE reports to total reports that can be made — there should only be a small part of the network that isn't cooperating.
- A SUCCESS report will positively effect the reputation of the corresponding Farmer, and likewise a FAILURE report will have a negative effect. Thus accurate reporting is encouraged to improve quality at the interest of the Client. These metrics can be used in combination with [SIP6](sip-0006.md) for selecting which Farmers to upload data to further encourge successful shard transfers.
- If unconfirmed FAILURE reports, or lack of reports, exceed a tolerable expected threshold of unconfirmed FAILURE per Client reporter, the Client account is flagged as having unexpected rates of FAILURE. The behavior of such a flag can be configurable. It could be handled so that any repeated FAILURE reports or UNKNOWN would be considered SUCCESS reports as to prevent abuse. For example in the scenario where a Client isn't reporting SUCCESS or FAILURE, it would be assumed to always have SUCCESS status. By reporting not only would it improve the quality, but it would also more accurately track bandwidth usage by accounting for failures.

### Exchange Report

TODO

### Storage Event

TODO

Reference Implementation
------------------------

TODO

Copyright
-------------

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).