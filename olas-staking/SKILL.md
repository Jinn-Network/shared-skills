---
name: olas-staking
description: Operate OLAS service staking safely across restake, migration, status checks, and activity-checker coordination. Public, reusable workflow without environment-specific secrets.
allowed-tools: Bash, Read, Edit, Write, Glob, Grep
user-invocable: true
---

# OLAS Staking Operations

Reusable runbook for service staking lifecycle management.

## Scope

- Restake evicted services
- Migrate between staking contracts
- Verify staking and service state
- Keep activity checker eligibility aligned

## Core Concepts

- **Service state** and **staking state** are different; inspect both
- Evicted services can usually be unstaked and restaked without full re-registration
- Contract ownership and Safe execution path determine who can call write methods
- Reward availability can gate staking actions

## Restake Flow (Evicted Service)

```text
1. unstake(serviceId)
2. approve(stakingContract, serviceId) on service registry NFT
3. stake(serviceId)
4. re-verify checker eligibility
```

## Migration Flow (Contract A -> Contract B)

```text
1. Unstake from source
2. If required: terminate/unbond/reconfigure service bond
3. Activate and register as needed
4. Deploy or reuse multisig according to target policy
5. Approve NFT to target staking contract
6. Stake in target
7. Reconcile checker and reward settings
```

## Status Checks

Use read calls for:

- staking state by service ID
- list of currently staked service IDs
- service state in registry
- NFT owner for service token

Treat “staked list membership + NFT ownership” as canonical confirmation.

## Activity Checker Coordination

After restake or migration:

- ensure service/multisig is eligible under checker rules
- ensure required throughput thresholds are configured
- verify eligibility with checker read methods

## Safe Transaction Guidance

- Route privileged staking actions through the expected Safe context
- If SDK gas estimation is unreliable, use explicit gas limits and deterministic signing flow
- Confirm nonce source consistency across providers

## Funding Model

Plan for:

- service security deposit
- per-agent bond requirements
- gas budget for full lifecycle actions
- rewards liquidity for staking contract

## Troubleshooting Patterns

1. **GS013-style reverts**: check signer context and Safe path
2. **No rewards available**: reward pool empty or not yet indexed
3. **Manager-only reverts**: wrong manager contract/address context
4. **RPC staleness**: nonce mismatch, delayed state visibility
5. **Path-coupled scripts**: env/config loaded from unintended root

## Security Rules

- No private key material in scripts or docs
- No personal machine paths in public repositories
- No operator-identifying internal wallet mapping unless intentionally public
- Use placeholders for all environment-specific values

## Operator Checklist

- [ ] Staking state verified
- [ ] Service state verified
- [ ] Eligibility checker verified
- [ ] Reward path verified
- [ ] Monitoring updated
