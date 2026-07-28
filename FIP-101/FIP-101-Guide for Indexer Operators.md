# FIP-101 Guide for Indexer Operators

> This guide is intended only as a reference for users.

## What Is an Indexer Operator?

An Indexer Operator is responsible for running a Fractal node and the open-source indexer, periodically submitting block proofs to the indexing coordinator, and receiving the operator’s share of rewards from the coordinator. Staking users can choose the operator’s node to participate in staking. When the node submits valid proofs and has a higher staking amount, it receives a higher weight in the reward distribution for the corresponding blocks.

## Deploy a Fractal Node and Lightweight Indexer

You can refer to the resources below to deploy your own node and configure your commission rate, reward wallet, and proof submission settings.

### Recommended Repositories

- Lightweight Indexer repository: https://github.com/fractal-bitcoin/fractal-indexer-deploy/
- Fractal open-source indexer: https://github.com/fractal-bitcoin/fractal-indexer
- Staking indexer repository: https://github.com/fractal-bitcoin/stake-indexer

### Operator Requirements

The operator must ensure that:

- the Fractal node remains properly synced;
- BRC-20 and staking-related indexed data are continuously updated;
- proofs are submitted within the valid window;
- the reward receiving address and the owner wallet are properly secured;
- the reward receiving address maintains sufficient FB balance to pay gas fees.

## Submit Proofs

Valid proofs are periodic runtime proofs submitted by an indexer. Only indexers that pass verification can participate in reward distribution.

You can refer to the following link for proof submission:

- https://github.com/fractal-bitcoin/fractal-indexer-deploy/tree/main/proof-publisher

Indexers are expected to submit a valid proof within 100 blocks.

### Late Submission Penalty

- Indexers must submit a valid hash proof within 720 blocks of block production.
- After this deadline, a delay is applied for every additional 120 blocks of delay. Rewards follow a progressive decay schedule, with lighter reductions early and steeper reductions as the delay increases.
- Rewards decay to zero after 1,440 blocks.

## Indexer Details Management

Visit https://index-mining-beta.fractalbitcoin.io/, connect your deployment address, switch to the Indexer Operator role, and then view your indexer information, stake addresses, commission settings, proof submission status, and operation history.

At the same time, make sure that the deployment information is displayed correctly.

## Indexer Commission Rules

Commission is the percentage of an index operator’s allocated FB staking rewards that is charged by the index operator as a fee.

- Indexers can set their commission rate between 0% and 15%.

### Commission Change Rules

- Commission changes take effect after 7 days.
- Once a commission change is submitted, no further changes can be made until the pending change takes effect.

## Reward Allocation Rules and Settlement

Only indexers that submit valid proofs for the allocated block can participate in reward allocation. Index Mining rewards are generated block by block and distributed according to effective staking shares.

### Public Testing Reward Release Schedule

Rewards during public testing follow the official Index Mining mechanism, allocation rules, and settlement rules, and the block reward remains 25 FB per block. To keep the rollout stable, rewards follow a linear release schedule; the proportion of each block reward distributed gradually increases over time until it reaches 100%.

- Stage 1: 30% to 60%, increasing by 7.5% each week
- Stage 2: 60% to 100%, increasing by 10% each week
- 100% release is expected to be reached by 2026-07-09

The following estimates are based on approximately 960 Index Mining blocks per day.

### Reward Allocation

For the Indexer Operator, each successful submission leads to block rewards being calculated and allocated to each indexer after a settlement window based on effective stake share. The reward for each indexer is split between the operator and the staker. Indexer Operators receive a share of the rewards as commission, with the commission rate set individually by each indexer. Stakers receive rewards proportional to their staking amount.

For stake participants, after users stake FB, they do not receive a fixed yield. Instead, they participate in Index Mining block reward distribution based on the operating status of the corresponding Indexer, the user’s effective staking share within that Indexer, and the Indexer’s effective share of total network staking.

The reward formula is:

$$
\text{User Reward} = \text{Block Released Reward} \times \frac{\text{Indexer Effective Stake}}{\text{Total Network Effective Stake}} \times (1 - \text{Indexer Commission Rate}) \times \frac{\text{User's Stake}}{\text{This Indexer's Raw Total Stake}}
$$

For more details on reward allocation, please refer to the document on rewards allocation.

### Reward Settlement Cycle

To avoid reward calculation errors caused by block rollbacks or indexing errors, Index Mining rewards are not claimable immediately after they are generated.

Index Mining rewards enter the reward calculation scope 1,000 blocks after the relevant block is produced. They become claimable 20,160 blocks after the relevant block is produced, or approximately 7 days later.

## Notes

- No security deposit is required, but invalid proofs do not participate in reward distribution for the corresponding block.
- The owner address and the reward address have different responsibilities; do not confuse them after registration.
- The stability of proof submission directly affects reward eligibility.
- The current implementation may differ from the operational rules during the testing period; development and external communication should distinguish between the current implementation and the testing plan.
