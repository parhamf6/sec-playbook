---
tags:
  - idor
---
pwnbox.io/challenges/invoice-loop
the core idea here is that a secure-looking token does not necessarily mean it's unpredictable.
at first, the share link looks like it uses a random 32-character token. but after creating an invoice and checking its ID, we can test whether the token is just the MD5 hash of that ID.
```
echo -n "1001" | md5sum
```
the output matches the token in the share link. since invoice IDs are numeric and predictable, we can generate hashes for other IDs and access invoices that do not belong to us.
main lesson here is that hashing a predictable value does not make it secret. use random, unguessable tokens for private links instead of deriving them from sequential IDs.