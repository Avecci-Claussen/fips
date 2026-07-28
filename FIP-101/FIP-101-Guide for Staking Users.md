# FIP-101 Guide for Staking Users

> This guide is intended only as a reference for users.

## Overview

FIP-101 introduces Fractal’s Standard Indexing Service, a framework designed to make indexing more open, permissionless, and economically aligned with the network. It enables indexers to participate in maintaining Fractal’s indexing layer, while FB holders can stake FB to support indexers. Index Mining is the block reward mechanism for this system, distributing FB rewards to eligible indexers and stake participants who help support the indexing network.

## Index Mining Staking Rewards

Rewards during public testing follow the official Index Mining mechanism and the block reward of 25 FB per block.

To keep the rollout stable, rewards follow a linear release schedule. The proportion of each block reward distributed gradually increases over time until it reaches 100%.

This means:

- Stage 1: the release percentage starts at 30% (7.5 FB per block) and increases with each block until it reaches 60%
- Stage 2: the release percentage starts at 60% (15 FB per block) and continues increasing until it reaches 100% (25 FB per block)

Please note that rewards from each block become claimable after a 7-day settlement period. As a result, rewards earned during the first 7 days after joining Stage 1 are not immediately available for claiming. Please wait for the settlement period to complete.

For more details, you can view the reward information here.

## How to Stake in the Index Mining Public Testing

Visit the Index Mining website at https://index-mining-beta.fractalbitcoin.io/ and connect your wallet.

On the homepage, you can view the current staking overview, including:

- active indexers;
- total FB staked;
- current allocated block reward;
- the indexer list;
- your personal staking details.

## Start Participating in Index Mining Staking

### 1. Browse the Indexer List

In the Indexer List panel, you can view all current indexers, along with their Stake Share and Fee Ratio.

- Stake Share refers to the proportion of FB staked on that indexer compared with the total FB staked across all indexers.
- Commission is the percentage of an index operator’s allocated FB staking rewards that is charged by the index operator as a fee.

You can click “View Details” on the right side of each indexer to see more information about that indexer.

> Note: If you are an index operator, you can refer to the guide for indexer operators to apply as one of the test index operators for Fractal Index Mining.

### 2. Stake FB and Earn

Choose the indexer you want to support, then click “Stake Now” on the right side of that indexer.

On the staking page:

1. Enter the amount of FB you want to stake.
2. Review the staking details carefully.
3. Click “Stake”.
4. Sign the transaction and complete the staking process.

#### Tips

- After staking is completed, the system transfers the staked FB to your dedicated staking address and records your association with the selected indexer on-chain.
- Minimum stake per address: 50 FB.
- Reward allocation takes place after a settlement window of 20,160 blocks (about 7 days). Users can claim rewards after the settlement cycle.

### 3. View and Claim Your Staking Rewards

Staking rewards must be claimed manually.

Click “My Staking” in the navigation bar to open your staking dashboard. There you can view:

- Reward History — FB staking rewards you have earned
- Operation History — your staking-related activity records

## How to Unstake

There is no lock-up period, and you can unstake at any time.

To unstake, simply transfer FB out of your staking address to any address. The system automatically detects the balance change and updates your staking status.

