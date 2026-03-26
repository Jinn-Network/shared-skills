---
name: olas-registry
description: Register and manage OLAS protocol units (components, agents, services) with correct metadata hashing, IPFS publishing, and on-chain verification. Sanitized for public use.
allowed-tools: Bash, Read, Edit, Write, Glob, Grep
---

# OLAS Registry Management

Public workflow for registering components, agents, and services on OLAS-compatible registries.

## Critical Hashing Rule

On-chain metadata hashes typically expect the raw digest from the IPFS multihash payload, not an arbitrary alternate hash.

### Correct pattern

1. Upload metadata JSON to IPFS
2. Decode CID/multihash payload
3. Extract expected digest bytes
4. Submit digest as `bytes32` where required

### Incorrect pattern

Do not hash the `ipfs://...` string itself and submit that as metadata hash.

## Metadata Schema Baseline

Use stable fields that clients/indexers can rely on:

- `name`
- `description`
- `code_uri`
- `image`
- `attributes` (including version)

Keep custom fields optional and additive.

## Registration Flow

### Components / Agents

- Build metadata
- Upload to IPFS
- Convert metadata CID to on-chain hash format
- Call registry manager create function
- Parse minted ID from transfer logs/events

### Services

- Build service config metadata
- Upload to IPFS
- Submit service creation using correct manager/token utility contracts
- Verify resulting `tokenURI` resolves metadata

## Verification

For each minted unit:

- retrieve `tokenURI(unitId)`
- fetch JSON payload
- verify required fields and version value
- confirm marketplace/indexer visibility

## Common Failure Modes

1. **Create-event parser mismatch**: parse transfer log fallback
2. **Zero-value bonds**: create call reverts
3. **Threshold mismatch**: multisig threshold outside accepted range
4. **Wrong token sentinel**: create call rejects configuration
5. **Ownership mismatch on updates**: hash updates must be called by unit owner context

## Public Documentation Rules

- Do not include personal machine paths
- Do not include private key handling details
- Do not publish internal wallet ownership mapping unless explicitly intended
- Prefer placeholders for chain-specific addresses in generic templates

## Minimal Command Pattern

```bash
# compile
yarn compile

# register units
yarn <register-command> --dry-run

# verify
yarn <verify-command>
```

Keep command names aligned with local project scripts while preserving this sequence.
