# hmac-feistel

A TypeScript implementation of an HMAC-based Feistel cipher using SHA-256.

## Overview

This library provides a Feistel network cipher that uses HMAC-SHA256 as the round function. It performs 16 rounds of encryption/decryption on 64-byte blocks using a 32-byte key.

## Requirements

- Bun runtime
- TypeScript 5+

## Installation

```bash
bun add github:shovon/hmac-feistel
```

## Usage

### Basic Example

```typescript
import { hmacFeistelForward, hmacFeistelBackward } from "hmac-feistel";

// Generate a 32-byte key
const key = new Uint8Array(32);
crypto.getRandomValues(key);

// Prepare a 64-byte block of data to encrypt
const data = new TextEncoder().encode(
  "abcdefghijklmnopqrstuvwxyz123456abcdefghijklmnopqrstuvwxyz123456"
);

// Encrypt
const encrypted = hmacFeistelForward(key, data);

// Decrypt
const decrypted = hmacFeistelBackward(key, encrypted);

const result = new TextDecoder().decode(decrypted);
console.log(result); // Original data
```

### Working with Hex

```typescript
// Convert encrypted data to hex
const encryptedHex = Array.from(encrypted)
  .map((b) => b.toString(16).padStart(2, "0"))
  .join("");

console.log("Encrypted (hex):", encryptedHex);
```

## API

### `hmacFeistelForward(inputKey, inputBlock)`

Encrypts a block using HMAC-based Feistel cipher.

**Parameters:**
- `inputKey: Uint8Array` - 32-byte encryption key
- `inputBlock: Uint8Array` - 64-byte block to encrypt

**Returns:** `Uint8Array` - Encrypted 64-byte block

**Throws:** Error if key is not 32 bytes or block is not 64 bytes

### `hmacFeistelBackward(inputKey, inputBlock)`

Decrypts a block using HMAC-based Feistel cipher.

**Parameters:**
- `inputKey: Uint8Array` - 32-byte decryption key (must match encryption key)
- `inputBlock: Uint8Array` - 64-byte encrypted block to decrypt

**Returns:** `Uint8Array` - Decrypted 64-byte block

**Throws:** Error if key is not 32 bytes or block is not 64 bytes

## How It Works

1. The 64-byte block is split into two 32-byte halves (L and R)
2. For each of 16 rounds:
   - A round key is derived using HMAC(masterKey, roundNumber)
   - The right half is processed through HMAC with the round key
   - The result is XORed with the left half to create the new right half
   - The halves are swapped
3. After 16 rounds, the halves are concatenated to produce the output

Decryption reverses this process by running the rounds in reverse order (15 to 0).

## Testing

Run the test suite:

```bash
bun test
```

## Security Considerations

- Always use cryptographically secure random keys (32 bytes)
- This is a block cipher - you'll need to implement a mode of operation (ECB, CBC, CTR, etc.) for data larger than 64 bytes
- Store keys securely and never hardcode them in your source code
- This implementation uses 16 rounds and HMAC-SHA256 for the round function

## License

Private project
