```
SIP: downloads
Title: Client Downloads and Uploads
Author: Braydon Fuller <braydon@storj.io>
        Gordon Hall <gordon@storj.io>
Status: Draft
Type: Standard
Created: 2016-11-09
```

Abstract
--------

Motivation
----------

This addition has the following improvements:

1. Simplifies client dependencies for downloads and uploads to HTTP, TLS and
   JSON *(and preferred crypto for file encryption)*.
2. Reduces network latency for file download pointer resolving by reducing the
   total number of requests.
3. Client authorizes downloads directly from farmers for improved distributed access
   to files and reduced load on the bridge servers.

Specification
-------------

The proposal extends the protocol with two new RPC message commands `UPLOAD` and `DOWNLOAD`
that will respond with content type "application/octet-stream" and accept "multipart/form-data"
for uploads. Authorization for using these methods can be made inflight and validated against the
corresponding contract `client_keys`.

Contracts are extended to include a new field for `client_keys`, that will be an array
of public keys that are authorized to transfer data. Here is the flow request flow for
downloading a file:

![/sip-downloads/flow-diagram.png](/sip-downloads/flow-diagram.png)

Backwards Compatibility
-----------------------

These additions can be applied to new contracts that are created such that existing methods for
data transfers can continue to function. It can also be possible to update existing contracts using
the `RENEW` command as specified in [SIP4](/sip-0004.md), to add `client_keys` to the contract.

Reference Implementation
------------------------
- https://github.com/braydonf/libstorj-c
- https://github.com/braydonf/core

References
----------