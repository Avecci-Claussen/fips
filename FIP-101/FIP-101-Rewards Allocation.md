# FIP-101 Rewards Allocation

This document explains the reward mechanism for the FIP-101 Index Mining proposal. It describes how Index Mining rewards are distributed among eligible indexers and stakers and how the mechanism evolves across public testing and full rollout.

## 1. Overview

The FIP-101 proposal integrates the Index Mining Service into Fractal’s block reward mechanism, forming a long-term and sustainable incentive system.

Index Mining Service block rewards are shared among all eligible indexers and stakers proportionally according to each participant’s effective stake share per block.

## 2. Fractal Block Reward Basics

Fractal’s block structure is designed as a 1:1:1 model:

- Merged Mining: 1
- Permissionless Mining: 1
- Index Mining: 1

Each Fractal block provides a reward of 25 FB until the next halving event at block height 2,100,000.

## 3. Index Mining Rewards

The official Index Mining block reward is 25 FB per block until the next halving event. This reward is shared among eligible indexers and stakers proportionally according to each participant’s effective stake share per block.

During public testing, Index Mining rewards follow a linear release schedule. The proportion of each block reward that is distributed gradually increases over time until it reaches 100% (25 FB).

Block rewards are first sent to a cold wallet address and are then allocated to indexing operators and stakers after a settlement window.

## 4. Public Testing Reward Mechanism

The public testing reward mechanism is based on the official Index Mining reward mechanism after launch, with phased adjustments. The core logic, including the staking mechanism, remains consistent with the long-term mechanism after the official launch on Fractal mainnet.

### 4.1 Stage 1

The release percentage starts at 30%, which corresponds to:

- $25 \times 30\% = 7.5$ FB per block

It increases gradually with each block until it reaches 60%:

- $25 \times 60\% = 15$ FB per block

### 4.2 Stage 2

The release percentage begins at 60% (15 FB per block) and continues increasing until it reaches 100%.

The 100% release target is expected to be reached by 2026-07-09.

Each stage is estimated to last approximately four weeks, providing sufficient time for testing and corrections. Stages may be extended if additional time is required.

Any unreleased rewards may be reserved for a long-term node incentive or subsidy pool after the official launch.

## 5. Rewards Settlement Period

Rewards from each block become claimable after a 7-day settlement period, equal to 20,160 blocks.

Rewards earned during the first 7 days after joining Stage 1 are not immediately available for claiming. Users must wait for the settlement cycle to complete before they can claim their rewards.

## 6. User Staking Yield Estimates

The following estimates assume:

- Staked amount: 100,000 FB
- Daily Index Mining blocks: 960
- Official Indexer commission: 10%
- Valid proof submission rate: 100%, with no penalty
- Reinvestment: not included
- APR formula: $\text{daily reward} \times 365 / \text{staked principal}$

### 6.1 Daily Reward Estimates

The table below shows estimated daily rewards in FB/day for a 100,000 FB stake under different network stake sizes and release rates.

| Total Network Stake | 30% | 37.50% | 45% | 52.50% | 60% | 70% | 80% | 90% | 100% |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 5m FB | 129.6 | 162.0 | 194.4 | 226.8 | 259.2 | 302.4 | 345.6 | 388.8 | 432.0 |
| 10m FB | 64.8 | 81.0 | 97.2 | 113.4 | 129.6 | 151.2 | 172.8 | 194.4 | 216.0 |
| 15m FB | 43.2 | 54.0 | 64.8 | 75.6 | 86.4 | 100.8 | 115.2 | 129.6 | 144.0 |
| 20m FB | 32.4 | 40.5 | 48.6 | 56.7 | 64.8 | 75.6 | 86.4 | 97.2 | 108.0 |
| 30m FB | 21.6 | 27.0 | 32.4 | 37.8 | 43.2 | 50.4 | 57.6 | 64.8 | 72.0 |

As shown in the table, when the total network stake is 20m FB, each 100,000 FB can receive approximately 108 FB in daily rewards after decentralized multi-node staking is officially launched.

### 6.2 APR Estimates

| Total Stake | 30% | 37.50% | 45% | 52.50% | 60% | 70% | 80% | 90% |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 5m FB | 47.30% | 59.13% | 70.96% | 82.78% | 94.61% | 110.38% | 126.14% | 141.91% |
| 10m FB | 23.65% | 29.57% | 35.48% | 41.39% | 47.30% | 55.19% | 63.07% | 70.96% |
| 15m FB | 15.77% | 19.71% | 23.65% | 27.59% | 31.54% | 36.79% | 42.05% | 47.30% |
| 20m FB | 11.83% | 14.79% | 17.74% | 20.70% | 23.65% | 27.59% | 31.54% | 35.48% |
| 30m FB | 7.88% | 9.86% | 11.83% | 13.80% | 15.77% | 18.40% | 21.02% | 23.65% |

## 7. Reward Distribution and Settlement

### 7.1 Stake Participants

In Stage 1 of public testing, all users can participate in Index Mining and earn rewards.

The system continuously generates Index Mining rewards on a per-block basis. Users participate in reward distribution by staking in support of a specific Indexer during both public testing and full rollout. Once the Indexer submits a valid proof, the reward for that block enters the distribution process.

The rewards for stake participants depend on:

- the Index Mining block reward release ratio;
- the user’s share of the total stake under that Indexer;
- the commission set by the Index Operator;
- whether the Index Operator submits a valid proof and whether a delayed submission penalty is triggered.

Specific reward details can be viewed on the detail page of the Index Operator that the user has staked with: https://index-mining-beta.fractalbitcoin.io/

### 7.2 Index Operators

In public testing Stage 2, anyone can register their own Indexer by adopting Fractal’s lightweight indexer design and joining Fractal’s data indexing network.

#### Participation Requirements

Index Operators are required to submit valid proofs on a regular basis. Only Index Operators that submit valid proofs for allocated blocks are eligible to participate in reward distribution.

#### Reward Distribution

After each successful proof submission, Index Mining block rewards are distributed to the Index Operator and its stakers.

#### Fee Ratio

An Index Operator may set its own fee ratio, which is the percentage of the Indexer’s allocated FB staking rewards charged by the Index Operator as a fee.

Indexers can set their commission rate between 0% and 15%.

Commission changes take effect after 7 days. Once a commission change is submitted, no further changes can be made until the pending change takes effect.

#### Delayed Submission Decay

Indexers must submit a valid hash proof within 720 blocks of block production.

After this deadline, a delay is applied for every additional 120 blocks of delay. Rewards follow a progressive delay schedule, with lighter reductions early and steeper reductions as the delay increases.

Rewards decay to zero at 1,440 blocks past the deadline.

#### Settlement Period

Each block reward becomes claimable after a 7-day settlement period. The records can be tracked on the My Staking page.

## 8. Public Testing Stages

### 8.1 Stage 1: Capped Staking with a Single Indexer

Index Mining public testing begins with a controlled setup using a single Indexer and capped staking participation.

This is the first phase of public testing. Its goal is to verify the stability of the Index Mining and staking mechanism under a controlled participation scale. After testing officially begins, the block rewards generated by each Index Mining block are fully allocated to a fixed cold wallet address and gradually released according to the corresponding ratio based on the total network staking amount.

#### Participation Rules

- Staking is open to all addresses.
- Minimum stake per address is 50 FB.
- There is no maximum stake limit.

### 8.2 Stage 2: Multi-Indexer Testing

The system expands to support multiple Indexers, allowing third-party Indexer participation to be tested in a broader environment.

This is the second phase of public testing. Its goal is to bring the Index Mining mechanism, reward distribution, and settlement mechanisms closer to the official launch state.

#### Participation Rules

- There is no staking amount limit per address.
- Third-party Indexers can register their own Indexer.

## 9. Full Rollout

After the official launch, the Index Mining reward mechanism enters long-term operation.

Each Index Mining block reward is 25 FB.

## 10. Notes

This page explains the reward mechanism for FIP-101 Index Mining rewards across different stages. The specific release schedules for Stage 1 and Stage 2 are subject to the applicable testing arrangements and official disclosures.
