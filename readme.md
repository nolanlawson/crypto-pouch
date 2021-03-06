crypto pouch [![Build Status](https://travis-ci.org/calvinmetcalf/crypto-pouch.svg)](https://travis-ci.org/calvinmetcalf/crypto-pouch)
===

encrypted pouchdb plugin

```js
db.crypto(password[, diffieHellman?]).then(function (publicKey) {
  // is all set up
});

db.removeCrypto();
// will no longer encrypt decrypt your data
```
If you replicate to another database it will decrypt before sending it to 
the external one, make sure that one has a password set as well if you want 
it encrypted too.

Currently encrypts with Chacha20-Poly1305, this may be changed 
to AES256-GCM when node 0.12.0 drops.

If you change the name of a document, it will throw an error when you try 
to decrypt it, if you manually move a document from one database to another 
it will not decrypt correctly.  If you need to decrypt it a file manually 
you will find a local doc named `_local/crypto` in the database, it has a field 
named salt which is a hex encoded buffer, run on your password with that as salt 
for 1000 iterations to generate a 32 byte (256 bit) key, that is the key 
for decoding documents. Each document has a 3 relevant fields, data, nonce, tag. 
nonce is the initialization vector to give to chacha20 in addition to the key 
you generated, pass the document id as additional authenticated data and the tag 
as the auth tag and then decrypt data.  If it throws an error then you either 
screwed up or somebody modified the data.

If the second argument is a string it is take to be a Diffie-Hellman ModP group 
and password is interpreted as a Diffie-Hellman public key, if so the public key 
for use with the database is returned, use that to calculate the shared secret 
which is needed for subsequently opening the data set.

