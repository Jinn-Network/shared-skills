---
name: deploy-staking
description: Deploy an OLAS-compatible staking program with a custom activity checker, register the associated agent, and nominate the staking contract for emissions. Designed for reusable deployment workflows without project-specific secrets or local-machine details.
allowed-tools: Bash, Read, Edit, Write, Glob, Grep
user-invocable: true
---

# Deploy OLAS Staking Contract

End-to-end deployment pattern for a new staking program using an activity checker and registry integration.

## Use Cases

- New staking deployment for a new agent ID
- Migration to a fresh staking contract when immutable parameters must change
- Activity checker logic update
- Fresh setup in a new environment

## Prerequisites

- Contract compilation succeeds
- Deployment wallet has gas on all required chains
- Required env vars are set by your deployment system (do not store credentials in the repo)
- Activity checker contract is implemented and tested

## Deployment Pipeline

### 1) Compile

```bash
yarn compile
```

### 2) Register Agent (if needed)

- Upload package and metadata to IPFS
- Convert metadata CID to the required on-chain hash format
- Create the agent entry in the relevant registry contract
- Capture new agent ID from logs/events

### 3) Deploy Activity Checker + Staking Contract

- Deploy checker with required constructor parameters
- Initialize staking instance through staking factory
- Persist deployed addresses in a deployment artifact (JSON)

### 4) Nominate for Emissions

- Submit nominee transaction to vote-weighting contract
- Verify nominee appears in governance UI/API

### 5) Update Runtime Configuration

Update all runtime and indexer configs that reference:

- Staking contract address
- Agent ID
- Service hash / metadata hash
- Any allowlists used by monitoring/indexing systems

### 6) Fund and Activate

- Fund staking contract rewards pool
- Allocate governance votes where required

## Parameter Planning

Plan these as immutable inputs:

- `maxNumServices`
- `rewardsPerSecond`
- `minStakingDeposit`
- `livenessPeriod`
- `timeForEmissions`
- `maxNumInactivityPeriods`
- checker-specific liveness ratio

Any change to immutable parameters requires a new deployment.

## Activity Checker Design Notes

- Keep checker logic minimal and auditable
- Avoid hidden permission paths unless governance requires them
- Ensure liveness math is deterministic and documented
- Add bounds checks to prevent invalid accounting deltas

## Recommended Tests

- Constructor validation
- Liveness pass/fail edge cases
- Nonce and delivery-count consistency checks
- Mainnet-fork integration for real contract interactions

## Common Failure Modes

1. **Zero metadata hash**: registry/verifier rejects empty hash
2. **ABI mismatch**: incorrect type widths alter function selector
3. **Path-dependent env loading**: scripts break across directories
4. **RPC instability**: nonce/read inconsistencies across providers
5. **Assuming mutability**: staking parameters are often immutable

## Operational Safety Rules

- Never store private keys, mnemonics, or decrypted wallet material
- Never commit machine-specific absolute paths
- Never hardcode operator-identifying wallet addresses unless explicitly public and required
- Prefer placeholders in public docs: `<CHAIN_RPC_URL>`, `<DEPLOYER_ADDRESS>`, `<STAKING_CONTRACT>`

## Deliverables Checklist

- [ ] Contracts deployed and verified
- [ ] Agent registered with correct dependencies
- [ ] Nomination completed
- [ ] Runtime/indexer config updated
- [ ] Rewards funded
- [ ] Monitoring and alerting pointed at new contract
