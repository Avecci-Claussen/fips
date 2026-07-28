# FIP-101 Technical Notes - Node Changes

> This proposal introduces a third block type to the Fractal Bitcoin network: the Indexer Block. It is designed to incentivize providers running Bitcoin Layer 2 data indexing services. The design replaces traditional Proof of Work with Schnorr signature verification for authorization, while using a cold-hot wallet separation architecture and a cursor range mechanism to support secure and decentralized incentive distribution.

## Abstract

This proposal introduces a third block type to the Fractal Bitcoin network—the Indexer Block—designed to incentivize providers running Bitcoin Layer 2 data indexing services. This block type replaces traditional Proof of Work with Schnorr signature verification, implementing secure and decentralized indexer incentive distribution through a cold-hot wallet separation architecture and cursor range mechanism.

## Motivation

### Background

Fractal Bitcoin currently supports two block types:

- Legacy blocks: traditional PoW mining, 45-second target interval
- AuxPoW blocks: merged mining, 90-second target interval

With the rapid growth of Layer 2 applications such as Ordinals inscriptions in the Bitcoin ecosystem, the demand for high-quality indexing services has increased significantly. These indexing services require:

- Continuous operation of full nodes and block data parsing
- Maintenance of large-scale index databases
- Provision of highly available API services

However, there is currently no effective on-chain incentive mechanism to support operators of these critical infrastructure services.

### Goals

This proposal aims to:

- Incentivize Indexing Services: create sustainable economic incentives for inscription data indexing service providers
- Decentralized Distribution: support fair participation of multiple indexing service providers through the cursor range mechanism
- Security Assurance: employ cold-hot wallet separation architecture to ensure authorization mechanism security
- Minimal Changes: reuse existing AuxPoW mechanisms to reduce implementation complexity and risk

## Specification

### Block Type Identification

Indexer blocks reuse the `VERSION_AUXPOW` flag (`0x100`), distinguished by chain ID:

| Block Type | Chain ID | `VERSION_AUXPOW` | `nVersion` Example |
| --- | --- | --- | --- |
| Legacy | - | No | `0x00000001` |
| AuxPoW | `0x2024` | Yes | `0x20240101` |
| Indexer | `0x2026` | Yes | `0x20260101` |

### Indexer Proof Structure (`CIndexerProof`)

Indexer blocks contain a 168-byte proof structure:

| Field | Size | Description |
| --- | --- | --- |
| `hotPubKey` | 32 bytes | Hot wallet x-only public key |
| `nAuthTimestamp` | 4 bytes | Authorization expiry timestamp (Unix) |
| `nRangeStart` | 2 bytes | Authorized cursor range start (inclusive) |
| `nRangeEnd` | 2 bytes | Authorized cursor range end (inclusive) |
| `coldSignature` | 64 bytes | Cold wallet Schnorr signature |
| `hotSignature` | 64 bytes | Hot wallet Schnorr signature |

### Signature Mechanism

#### Cold Wallet Signature

```text
message = SHA256(hotPubKey || nAuthTimestamp || nRangeStart || nRangeEnd)
coldSignature = SchnorrSign(coldPrivKey, message)
```

#### Hot Wallet Signature

```text
message = blockHash (Double-SHA256 of block header)
hotSignature = SchnorrSign(hotPrivKey, message)
```

### PoW Hash

Indexer blocks still require proof of work, using the following hash:

```text
powHash = SHA256(hotPubKey || blockheader)
```

- `hotPubKey` binds the PoW work to a specific hot wallet, preventing work theft.
- `blockheader` contains the nonce and time used for mining adjustment.
- Authorization parameters (`nAuthTimestamp`, `nRangeStart`, `nRangeEnd`) are already verified through the cold wallet signature, so they do not need to be included in the PoW hash.

### Cursor Range Mechanism

The cursor value is calculated from the lower 2 bytes of the previous block hash:

```text
cursor = prevBlockHash[0] | (prevBlockHash[1] << 8)
```

Range: `0` to `65535`.

Indexing service providers are authorized a range `[nRangeStart, nRangeEnd]` and can only produce blocks when the cursor falls within that range.

Example: three providers each allocated one-third of the range:

- Provider A: `[0, 21844]` ≈ `33.3%`
- Provider B: `[21845, 43689]` ≈ `33.3%`
- Provider C: `[43690, 65535]` ≈ `33.3%`

### Validation Rules

#### CheckProofOfWork Phase (Context-free)

1. Verify `Chain ID == 0x2026`
2. Verify `indexerProof` exists
3. Verify authorization is not expired: `blockTime <= nAuthTimestamp`
4. Verify the cursor is within the authorized range: `nRangeStart <= cursor <= nRangeEnd`
5. Verify the PoW hash: `GetProofOfWorkHash(header) < target` where `nBits = powLimit`
6. Verify the cold wallet signature is valid
7. Verify the hot wallet signature is valid

#### ContextualCheckBlockHeader Phase (Context-required)

1. Verify block height is greater than or equal to the activation height (`1,500,000`)
2. Verify the previous block is not an Indexer block (no consecutive blocks)
3. Verify the quantity constraint: the Indexer count cannot exceed both Legacy and AuxPoW counts

### Quantity Constraint

Let the post-activation block statistics be:

- $L$ = Legacy block count
- $A$ = AuxPoW block count
- $I$ = Indexer block count

A new Indexer block is rejected if and only if $I > L$ and $I > A$.

This constraint ensures the three block types trend toward a `1:1:1` ratio.

### Difficulty Adjustment

Indexer blocks are treated as Legacy blocks in ASERT difficulty calculation:

- Use the Legacy target interval (45 seconds)
- Counted in Legacy block statistics
- `nBits` is set to `powLimit`, and PoW verification uses the hash calculated by `GetProofOfWorkHash`

## Rationale

### Why Reuse `VERSION_AUXPOW`

- Chain ID already exists in `nVersion`, so no new flag bits are needed
- Maintains a simple version number structure
- Reuses the existing serialization mechanism
- Reduces implementation risk

### Why Use Cold-Hot Wallet Separation

- Security: the cold wallet private key is stored offline and never touches the network
- Flexibility: hot wallet authorization can have an expiry time, limiting the impact if leaked
- Manageability: node maintainers can adjust authorizations at any time

### Why Use the Cursor Range Mechanism

- No block height is required; validation can be completed in `CheckProofOfWork`
- Natural randomness: it is based on the previous block hash and is therefore unpredictable
- Flexible allocation: it supports arbitrary ratio distribution
- No single point of failure: if one provider goes offline, others are unaffected

### Why Indexer Blocks Use the Legacy Difficulty Track

Indexer blocks are treated as Legacy blocks in difficulty calculation for the following reasons:

- Keep the AuxPoW track stable: AuxPoW blocks are produced by large mining pools through merged mining; their difficulty adjustment should only reflect changes in merged mining hashrate
- Incentive balance: Indexer blocks share the 45-second target interval with Legacy blocks; counting them in the Legacy track maintains competitive balance between the two
- Simplified implementation: no need to create a third difficulty track for Indexer blocks

## Security Considerations

### Authorization Security

- Cold wallet offline: the cold wallet private key never touches the network and is only used for issuing authorizations
- Authorization expiry: each authorization has an expiry time, limiting the impact of leaks
- Range limitation: authorizations are only valid for specific cursor ranges, further limiting risk

### Abuse Prevention

- Quantity constraint: Indexer blocks cannot exceed both Legacy and AuxPoW blocks, preventing a single entity from gaining network control
- No consecutive blocks: two consecutive Indexer blocks are not allowed, ensuring other miners have block production opportunities
- Cursor randomness: the cursor is based on the previous block hash and cannot be predicted or manipulated

### Attack Vector Analysis

| Attack Type | Mitigation |
| --- | --- |
| Cold wallet leak | Can replace the cold wallet public key via hard fork |
| Hot wallet leak | Expiry mechanism limits impact; can immediately revoke and re-authorize |
| Provider collusion | Quantity constraint ensures Indexer blocks do not exceed one-third |
| Timestamp manipulation | Authorization expiry check is performed in `CheckProofOfWork` |

## Backward Compatibility

This proposal is a hard fork. After activation height `1,500,000`:

- Old version nodes cannot parse Indexer blocks
- Old version nodes will reject chains containing Indexer blocks
- All nodes must upgrade before activation

## Activation

- Activation Height: `1,500,000`
- Activation Method: hard fork, height-based activation

## Reference Implementation

See the Fractal Bitcoin codebase:

- `src/primitives/indexer.h` — Indexer proof data structure
- `src/primitives/indexer.cpp` — Indexer proof implementation
- `src/validation.cpp` — Validation logic
- `src/rpc/indexer_miner.cpp` — RPC interface
