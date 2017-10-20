```
SIP: ?
Title: Public and Shared Files
Author: Braydon Fuller <braydon@storj.io>
Status: Draft
Type: Standard
Created: 2017-10-20
```

Abstract
--------
This document details a proposal for public and shared files on the Storj network.

Motivation
----------
- Sending a large amount of authenticated data to many people quickly (e.g. software, security or gaming updates).
- Sending data between businesses securely and quickly.

Specification
-------------

### User Public Key
   - A user Encryption Key, as described in [SIP5](https://github.com/Storj/sips/blob/master/sip-0005.md), will have a new derived key index for the purpose of generating a private and public keypair for sharing and signing files with [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), these can be referenced as the User Private Key and User Public Key. The User Public Key will be then be associated with the user on the Bridge API.

### Bridge Tokens
   - Bridge API will have token based authentication for resources, such as files, that give authorization to request pointers for those files with caveats (e.g. 20 requests)

### Public Files
  - The new User Private Key is used for signing public files so that those downloading can verify the authenticity and integrity
  - The signature will be on the [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) of the file, as described in [SIP5](https://github.com/Storj/sips/blob/master/sip-0005.md), and the signature will be included with the file meta data
  - Public files are made distinct from user private files on a Bridge API, e.g. instead of `/buckets/<bucket-id>/files/<file-id>` that's used for encrypted files for a user, it would be `/public/<user-name>/files/<file-id>?token=<20-bytes>`, or with buckets `/public/<user-name>/buckets/<bucket-id>/files/<file-id>?token=<20-bytes>`
  - Public files can have a URL scheme `storj://<bridge-url>/public/<user-name>/files/<file-id>?token=<20-bytes>` for the purposes of sharing
  - The existing `index`, used for key derivation, will continue to be used. However as the file is public it will indicate that a client side Encryption Key is not needed, e.g. a null key derived at the `index`.

### Shared Files
  - The public key for each user can be used for sharing files with other users by using [Diffie-Hellman key exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
  - These files are also made distinct by using a different Bridge URL scheme, e.g. `/shared/<user-name>/files/<file-id>` or `/shared/<user-name>/buckets/<bucket-id>/files/<file-id>`
  - Shared files will be available for both users, and the file meta data will include both public keys for the purpose of generating the private key used of the file. An HMAC will still authenticate the file as described in [SIP5](https://github.com/Storj/sips/blob/master/sip-0005.md).
  - Users can subscribe to events to be notified when new shared files are available

Reference Implementation
------------------------

TODO

References
--------------
- https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm
- https://github.com/Storj/sips/blob/master/sip-0005.md
- https://en.wikipedia.org/wiki/Hash-based_message_authentication_code
- https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange


Copyright
---------

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
