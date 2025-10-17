## Assignment A: P2PKH Script Analysis

### 1. Opcode Breakdown

Script: `OP_DUP OP_HASH160 <PubKeyHash> OP_EQUALVERIFY OP_CHECKSIG`

- **`OP_DUP`**: Duplicates the top stack item (the public key), so it appears twice.
- **`OP_HASH160`**: Hashes the top item using SHA256 followed by RIPEMD160 (producing the Bitcoin address format).
- **`<PubKeyHash>`**: Pushes the expected 20-byte public key hash (from the recipient’s address) onto the stack.
- **`OP_EQUALVERIFY`**: Compares the computed hash with the expected hash. If they match, both are removed; if not, the script fails immediately.
- **`OP_CHECKSIG`**: Verifies that the signature (from the unlocking script) is valid for the remaining public key. Returns true only if valid.

### 2. Data Flow Diagram

Initial stack (from scriptSig): `[signature, publicKey]`

1. After `OP_DUP`: `[signature, publicKey, publicKey]`  
2. After `OP_HASH160`: `[signature, publicKey, HASH160(publicKey)]`  
3. After pushing `<PubKeyHash>`: `[signature, publicKey, actualHash, expectedHash]`  
4. After `OP_EQUALVERIFY` (success): `[signature, publicKey]`  
5. After `OP_CHECKSIG` (success): `[]` → script succeeds

### 3. What Happens if Signature Verification Fails?

If `OP_CHECKSIG` fails:
- The script returns `false`.
- The transaction is **invalid** and rejected by the network.
- The UTXO **cannot be spent** using that signature/public key pair.

### 4. Security Benefits of Hash Verification

- **Privacy**: Public key is not revealed until spend time.
- **Address abstraction**: Users share only hashes (addresses), not full public keys.
- **Integrity**: Ensures the spender’s public key matches the intended recipient.
- **Quantum resistance (partial)**: Delays public key exposure, reducing window for attacks.
