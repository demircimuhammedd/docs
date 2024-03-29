---
title: Sign & Verify Message
---

The following example shows how to sign and verify a message with NaCl (pronounced "salt"), which is a Networking and Cryptography library used in util-crypto.

```javascript
const {
  stringToU8a,
  u8aToHex
} = require('@polkadot/util');
const {
  naclEncrypt,
  ed25519PairFromSeed,
  ed25519Sign,
  ed25519Verify,
  randomAsU8a
} = require('@polkadot/util-crypto');

async function main () {
  // Create account seed for an "Alice" account (generally rather use mnemonics)
  const seedAlice = 'Alice'.padEnd(32, ' ');

  // Generate new public/secret keypair for Alice from the supplied seed
  const { publicKey, secretKey } = ed25519PairFromSeed(stringToU8a(seedAlice));

  // Encrypt message. Create Uint8Array's filled with random bytes of specified length
  const secret = randomAsU8a(32);
  const messagePreEncryption = stringToU8a('please send me DOTs');
  const noncePreEncryption = randomAsU8a(24);

  const { encrypted } = naclEncrypt(messagePreEncryption, secret, noncePreEncryption);

  // Sign the message with a valid signature
  const messageSignature = ed25519Sign(encrypted, secretKey);

  console.log(`Message signature: ${u8aToHex(messageSignature)}`);

  // Validate that the message was correctly signed
  const isValidSignature = ed25519Verify(encrypted, messageSignature, publicKey);

  console.log(`Was the message correctly signed? ${isValidSignature}`);
}

main().catch(console.error).finally(() => process.exit());
```

