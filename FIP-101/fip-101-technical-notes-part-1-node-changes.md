# FIP-101 Technical Notes - Part 1: Node Changes

## Abstract

This proposal introduces a third block type to the Fractal Bitcoin network—the Indexer Block—designed to incentivize providers running Bitcoin Layer 2 data indexing services. This block type replaces traditional Proof of Work with Schnorr signature verification, implementing secure and decentralized indexer incentive distribution through a cold-hot wallet separation architecture and cursor range mechanism.

## Motivation

### Background

Fractal Bitcoin currently supports two block types:
- **Legacy blocks**: Traditional PoW mining, 45-second target interval
- **AuxPoW blocks**: Merged mining, 90-second target interval

With the rapid growth of Layer 2 applications such as Ordinals inscriptions in the Bitcoin ecosystem, the demand for high-quality indexing services has increased significantly. These indexing services require:
- Continuous operation of full nodes and block data parsing
- Maintenance of large-scale index databases
- Provision of highly available API services

However, there is currently no effective on-chain incentive mechanism to support operators of these critical infrastructure services.

### Goals

This proposal aims to:

1. **Incentivize Indexing Services**: Create sustainable economic incentives for inscription data indexing service providers
2. **Decentralized Distribution**: Support fair participation of multiple indexing service providers through the cursor range mechanism
3. **Security Assurance**: Employ cold-hot wallet separation architecture to ensure authorization mechanism security
4. **Minimal Changes**: Reuse existing AuxPoW mechanisms to reduce implementation complexity and risk

## Specification

### Block Type Identification

Indexer blocks reuse the `VERSION_AUXPOW` flag (0x100), distinguished by Chain ID:

| Block Type | Chain ID | VERSION_AUXPOW | nVersion Example |
|------------|----------|----------------|------------------|
| Legacy     | -        | No             | 0x00000001       |
| AuxPoW     | 0x2024   | Yes            | 0x20240101       |
| Indexer    | 0x2026   | Yes            | 0x20260101       |

### Indexer Proof Structure (CIndexerProof)

Indexer blocks contain a 168-byte proof structure:

| Field          | Size     | Description                              |
|----------------|----------|------------------------------------------|
| hotPubKey      | 32 bytes | Hot wallet x-only public key             |
| nAuthTimestamp | 4 bytes  | Authorization expiry timestamp (Unix)    |
| nRangeStart    | 2 bytes  | Authorized cursor range start (inclusive)|
| nRangeEnd      | 2 bytes  | Authorized cursor range end (inclusive)  |
| coldSignature  | 64 bytes | Cold wallet Schnorr signature            |
| hotSignature   | 64 bytes | Hot wallet Schnorr signature             |

### Signature Mechanism

**Cold Wallet Signature**:
```
message = SHA256(hotPubKey || nAuthTimestamp || nRangeStart || nRangeEnd)
coldSignature = SchnorrSign(coldPrivKey, message)
```

**Hot Wallet Signature**:
```
message = blockHash (Double-SHA256 of block header)
hotSignature = SchnorrSign(hotPrivKey, message)
```

### PoW Hash

Indexer blocks still require proof of work, using the following hash:

```
powHash = SHA256(hotPubKey || blockheader)
```

- `hotPubKey` binds the PoW work to a specific hot wallet, preventing work theft
- `blockheader` contains nonce and time for mining adjustment
- Authorization parameters (nAuthTimestamp, nRangeStart, nRangeEnd) are already verified through the cold wallet signature, so they don't need to be included in the PoW hash

### Cursor Range Mechanism

The cursor value is calculated from the lower 2 bytes of the previous block hash:

```
cursor = prevBlockHash[0] | (prevBlockHash[1] << 8)
Range: 0 ~ 65535
```

Indexing service providers are authorized a range `[nRangeStart, nRangeEnd]` and can only produce blocks when the cursor falls within that range.

**Example**: Three providers each allocated 1/3 of the range
- Provider A: [0, 21844] ≈ 33.3%
- Provider B: [21845, 43689] ≈ 33.3%
- Provider C: [43690, 65535] ≈ 33.3%

### Validation Rules

#### CheckProofOfWork Phase (Context-free)

1. Verify Chain ID == 0x2026
2. Verify indexerProof exists
3. Verify authorization not expired: blockTime <= nAuthTimestamp
4. Verify cursor within authorized range: nRangeStart <= cursor <= nRangeEnd
5. Verify PoW Hash: GetProofOfWorkHash(header) < target (nBits = powLimit)
6. Verify cold wallet signature is valid
7. Verify hot wallet signature is valid

#### ContextualCheckBlockHeader Phase (Context-required)

1. Verify block height >= activation height (1,500,000)
2. Verify previous block is not an Indexer block (no consecutive blocks)
3. Verify quantity constraint: Indexer count cannot exceed both Legacy and AuxPoW counts

### Quantity Constraint

Let post-activation block statistics be:
- L = Legacy block count
- A = AuxPoW block count
- I = Indexer block count

A new Indexer block is rejected if and only if `I > L && I > A`.

This constraint ensures the three block types trend toward a 1:1:1 ratio.

### Difficulty Adjustment

Indexer blocks are treated as Legacy blocks in ASERT difficulty calculation:
- Use Legacy target interval (45 seconds)
- Counted in Legacy block statistics

Indexer blocks have nBits set to powLimit, and PoW verification uses the hash calculated by `GetProofOfWorkHash`.

## Rationale

### Why Reuse VERSION_AUXPOW

1. Chain ID already exists in nVersion, no new flag bits needed
2. Maintains simple version number structure
3. Reuses existing serialization mechanism
4. Reduces implementation risk

### Why Cold-Hot Wallet Separation

1. **Security**: Cold wallet private key stored offline, never touches network
2. **Flexibility**: Hot wallet authorization can have expiry time, limited impact if leaked
3. **Manageability**: Node maintainers can adjust authorizations at any time

### Why Cursor Range Mechanism

1. **No block height needed**: Validation can be completed in CheckProofOfWork
2. **Natural randomness**: Based on previous block hash, unpredictable
3. **Flexible allocation**: Supports arbitrary ratio distribution
4. **No single point of failure**: One provider going offline doesn't affect others

### Why Indexer Blocks Use Legacy Difficulty Track

Indexer blocks are treated as Legacy blocks in difficulty calculation for the following reasons:

1. **Keep AuxPoW track stable**: AuxPoW blocks are produced by large mining pools through merged mining; their difficulty adjustment should only reflect changes in merged mining hashrate
2. **Incentive balance**: Indexer blocks share the 45-second target interval with Legacy blocks; counting them in the Legacy track maintains competitive balance between the two
3. **Simplified implementation**: No need to create a third difficulty track for Indexer blocks

## Security Considerations

### Authorization Security

- **Cold wallet offline**: Cold wallet private key never touches the network, only used for issuing authorizations
- **Authorization expiry**: Each authorization has an expiry time, limiting the impact of leaks
- **Range limitation**: Authorizations are only valid for specific cursor ranges, further limiting risk

### Abuse Prevention

- **Quantity constraint**: Indexer block count cannot exceed both Legacy and AuxPoW, preventing single entity network control
- **No consecutive blocks**: Two consecutive Indexer blocks not allowed, ensuring other miners have block production opportunities
- **Cursor randomness**: Cursor is based on previous block hash, cannot be predicted or manipulated

### Attack Vector Analysis

| Attack Type            | Mitigation                                                              |
|------------------------|-------------------------------------------------------------------------|
| Cold wallet leak       | Can replace cold wallet public key via hard fork                        |
| Hot wallet leak        | Expiry mechanism limits impact; can immediately revoke and re-authorize |
| Provider collusion     | Quantity constraint ensures Indexer blocks don't exceed 1/3             |
| Timestamp manipulation | Authorization expiry check performed in CheckProofOfWork                |

## Backward Compatibility

This proposal is a **hard fork**. After activation height 1,500,000:
- Old version nodes cannot parse Indexer blocks
- Old version nodes will reject chains containing Indexer blocks
- All nodes must upgrade before activation

## Activation

- **Activation Height**: 1,500,000
- **Activation Method**: Hard fork, height-based activation

## Reference Implementation

See Fractal Bitcoin codebase:
- `src/primitives/indexer.h` - Indexer proof data structure
- `src/primitives/indexer.cpp` - Indexer proof implementation
- `src/validation.cpp` - Validation logic
- `src/rpc/indexer_miner.cpp` - RPC interface

