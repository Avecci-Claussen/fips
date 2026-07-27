# FIP-101 Community Feedback & Reply

> Thanks to everyone who shared feedback on FIP-101.
>
> We carefully reviewed every comment. Most of the feedback focused on the rationale and implementation details of the proposal. To address this, we prepared a letter to the community outlining the motivation and design rationale behind the proposal.

## Abstract

The current indexing landscape on Fractal is fragmented, which leads to high costs for application integration and ecosystem collaboration. At the same time, as a piece of critical infrastructure, indexing lacks stable incentives. Over the long term, this can easily result in supply being concentrated among a small number of providers and in ecosystem lock-in. To address this, we propose promoting an open-source, permissionless, replaceable indexing service system with standardized outputs, and adjusting the block reward allocation structure from the current 1:2 ratio (merged mining : permissionless mining) to 1:1:1 (merged mining : permissionless mining : index mining). This adjustment does not change FB’s total issuance or emission schedule. The goal is to achieve a 1:1:1 distribution in the long-run statistical sense.

Implementation details will be released in follow-up publications. The mainnet activation height and timeline will be clarified later and announced in advance.

## Why We Want to Do This

### Current Problems

#### Indexing Is Too Fragmented

There are already many indexing solutions on Fractal, some commercial and some open-source. When indexing becomes the primary gateway for applications to read the on-chain world, inconsistencies between indexers become amplified into ecosystem-wide friction:

- For the same on-chain data, different indexers may return different fields, definitions, and results.
- Application teams must do extensive adaptation work, leading to higher maintenance costs and a greater likelihood of errors.
- The ecosystem struggles to form unified developer practices and reusable infrastructure.

In the long run, this fragmentation can drag the ecosystem into a pattern of “reinventing the wheel and repeated reconciliation”: slower development, higher maintenance costs, fragmented user experience, and ultimately weaker large-scale collaboration and growth across the Fractal ecosystem.

#### Indexing Is Foundational Infrastructure but Lacks Sustainable Supply

For applications and the ecosystem, “data availability, queryability, and reusability” are baseline requirements. But if indexing is carried for a long time mainly by a small number of teams or services, the ecosystem can easily become dependent and locked in. If a service becomes unstable, stops being maintained, or changes its rules, neither availability nor neutrality can be reliably guaranteed, let alone true decentralization and long-term sustainability. What we need is not to “maintain the status quo of small, non-replaceable supply,” but a mechanism that can run over the long term: standardization, replaceability, open participation, and sustainable incentives.

### Why Now Is the Right Time

Over the past two quarters, the UniSat team has done substantial refactoring work on the indexer system it maintains. Memory usage has been reduced by about 80%, initial synchronization can now complete within 24 hours, and hardware requirements have been significantly lowered. This means ordinary users may be able to run this indexing service without enterprise-grade servers.

Technology is now mature, and ecosystem demand is becoming increasingly urgent. If we do not launch a standardized solution soon, fragmentation will only worsen, and unifying standards later will become much harder. Now is the best window to push this forward.

## What We Want to Do

### Core Idea: Make Standardized Indexing an Ecosystem-Level Piece of Infrastructure

We want to elevate indexing mining to the same level of importance as mining, so it is no longer an auxiliary service maintained by “whoever has time” or “whoever subsidizes it,” but a long-term public capability within the Fractal ecosystem that anyone can participate in providing.

It is already clear to everyone that merged mining provides security, and permissionless mining provides open participation. But for applications to truly run, there must be a stable, unified, reusable data access layer. Indexing provides not just “convenience,” but data availability and composability: whether developers can reliably read what happened on-chain, build features using consistent definitions, and switch smoothly among services often depends on indexing.

Therefore, we want to promote a community-usable standardized indexing service system with a very clear direction:

- Open source: core code, specifications, and implementations should be as open and transparent as possible for auditing, reuse, and secondary development.
- Permissionless operation: anyone can run an indexing instance without approval or whitelisting, preventing “gateways” from being controlled by a few parties.
- Outputs as standardized as possible: the same class of on-chain behavior should be expressed with consistent fields, definitions, and semantics, reducing “different narratives for the same transaction.”
- Replaceable: applications and the ecosystem can migrate across different indexing instances and implementations without being locked into one team or one service.

Our goal is not to “force everyone to use a single indexer,” but to give the ecosystem a shared language and interchangeable implementations. You can choose the provider you trust, or run your own, but everyone speaks the same data definitions externally.

### Bring Indexing Into Block Reward Allocation

Since indexing is public foundational infrastructure for the ecosystem, it should have a long-term, sustainable supply mechanism. The most direct way is to incorporate indexing into chain-level incentives so that, like mining, it becomes a base network function rather than something kept alive by a team’s enthusiasm or short-term subsidies.

We plan to adjust the structure of block rewards:

- Current: 1:2 (merged mining : permissionless mining)
- Proposed: 1:1:1 (merged mining : permissionless mining : index mining)

Two points must be made clear:

1. Total issuance and emission schedule do not change. FB’s total supply, halving schedule, and other core monetary parameters remain unchanged; we are only reallocating the same block reward across different network roles.
2. This is a long-run statistical target. We aim for a distribution that approaches 1:1:1 over a longer time window, rather than mechanically splitting every single block equally, to avoid excessive rigidity that could harm operational flexibility.

Under this design, we also consider three things simultaneously: open participation, fund security, and system stability.

#### Open Participation: Anyone Can Run It

Indexing is permissionless and fully open: individuals, teams, miners, or mining pools can run indexing instances. The indexing software will be open-sourced and accompanied by standards and documentation. No applications or approvals are needed; anyone providing service according to the specification can participate in reward allocation.

Users who do not want to run their own nodes can also participate by staking FB to an indexing node and sharing that instance’s rewards proportionally.

#### Fund Security: Non-Custodial Staking, Exit Anytime, No Slashing

Staking uses a Taproot-based non-custodial mechanism. Assets remain controlled by the user’s own private keys, and operators cannot move them. Users can exit or switch indexing instances at any time. We do not introduce slashing: if an indexing service has issues, the principal is not confiscated; at most, rewards during that period are reduced or paused, lowering the participation threshold and risk.

#### System Stability: Indexer Failures Do Not Affect Block Production

Indexer reward settlement is asynchronous and not on the critical path of block production. Even if a large number of indexers go offline, the network can still mine and produce blocks normally. After service recovers, it can catch up and settle retroactively; the long-run distribution still targets 1:1:1.

## Three WHYs

### Why Change the Reward Structure to 1:1:1?

The most directly impacted group by this adjustment is permissionless-mining miners. Their share of block rewards will drop from 2/3 to 1/3. We make this trade-off because we want the reward structure to better match Fractal’s long-term needs and network division of labor.

From practical observation, Fractal’s security is primarily guaranteed by merged mining, inheriting Bitcoin’s hashpower. Permissionless mining is more focused on providing open participation and decentralization. Meanwhile, for applications to truly run, there must be a stable, unified, reusable data access layer. Therefore, by introducing indexer incentives, we hope to expand the network’s benefit structure from a single block-production return to a multidimensional participation path of “mining + indexing (and staking participation),” forming a more long-term, continuous incentive source.

Index mining contributes critically to ecosystem growth: every application and every developer needs indexing services. Without reliable indexing, the ecosystem cannot develop healthily. From a resource-allocation perspective, allocating part of rewards to indexing services better serves the ecosystem’s long-term interests.

In addition, this change does not necessarily mean “miners will definitely earn less.” Miners and mining pools can also participate in the indexing system: they already have nodes and operational capabilities, and the marginal cost of additionally running an indexer is usually lower. If they participate in indexer supply, their revenue structure expands from “mining only” to “mining + indexing,” and actual returns depend on participation level and operational efficiency.

### Why Not Make It an “Auxiliary Mining Function” Without Changing the Original Allocation?

One core issue this proposal aims to solve is that indexing lacks an independent, long-term, sustainable incentive source. If indexing is merely an “auxiliary function” and the reward structure remains unchanged, indexer supply will naturally be squeezed into an optional item: when markets are weak it shrinks, when subsidies end it stops, and the ecosystem returns to the old state of “a few teams carrying the load.” To force an indexing mechanism in without changing allocation, more complex rules are often required, bringing a longer trial-and-error cycle and greater uncertainty.

It must be emphasized that independent incentives do not mean “excluding miners.” Miners and mining pools can still join the indexing system as open participants and may even have a cost advantage due to existing infrastructure.

### Why Switch Directly?

We prefer to switch directly once preparation is complete, rather than using a staged, smooth transition.

A staged transition appears gentler and reduces the immediate impact on miners. But in practice it introduces more problems. First, technical complexity increases significantly: we would need to design a mechanism to adjust ratios gradually, which not only extends development time but also increases the risk of errors. More importantly, during the transition, parameters keep changing, and participants cannot form stable expectations. Miners do not know what their revenue will be next month; people who want to participate in indexing do not know when the best time is. This uncertainty makes decision-making harder. The benefit of a direct switch is clarity. We will announce the activation height in advance to give everyone time to prepare. At that height, the new rules take effect. Everyone knows the exact timing and what will change, and can prepare accordingly—whether adjusting mining strategy or preparing to participate in indexing services.

---

*2026-01-24*