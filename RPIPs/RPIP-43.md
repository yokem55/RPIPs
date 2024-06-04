---
rpip: 43
title: Megapools
description: A single contract per node operator that can be used as an Ethereum withdrawal address for multiple validators. Also allows for ETH-only node operation.
author: Valdorff (@Valdorff)
discussions-to: TBD
status: Draft
type: Protocol
category: Core
created: 2024-03-05
requires: 30, eip-4788
tags: tokenomics-2024, tokenomics-content
---

## Abstract
This proposal drastically reduces the gas to add validators and distribute rewards for them. It does so by creating a single contract that can be used as the Ethereum withdrawal address for any number of validators. In addition, the contract will facilitate the application of node-level penalties, as opposed to the current state where penalties are assigned and processed at the minipool level. Note that this proposal also enables ETH-only Node operation.

## Specification

### Megapool Contracts

This specification introduces a type of smart contract called a "megapool" that
SHALL serve as the target of Beacon Chain withdrawal credentials for Rocket Pool
validators deposited after this RPIP is implemented.

- A Node Operator SHALL be able to deploy at most one megapool contract
    - This MAY be combined with the deposit of their first validator
- Megapool contracts MUST be upgradable using a proxy pattern; a megapool's
  proxy implementation contract SHALL be called the megapool's "delegate"
- Legacy minipool deposits (ie, deposits of validators with withdrawal
  credentials that are not a megapool) SHALL be disabled
- Solo migration into legacy minipools (ie, promotion of vacant minipools)
  SHALL be disabled; this MAY be achieved by disabling creation of vacant
  minipools

### Validator Management

Megapools keep track of the set of validators associated with them and the
status of each validator. The possible statuses for validators associated with
megapools SHALL include at least the following:
  - Prestaked: a Node Operator has added this validator to their megapool, but
               the validator has not yet had its full activation balance
               deposited to the beacon chain
  - Staked:    this validator has been added to the megapool and is in the
               `pending_queued` or later state on the beacon chain
  - Dissolved: this validator's withdrawal credentials have been discovered to
               be incorrect (ie, not this megapool)

Node operators can manage the set of validators in their megapool:

- A Node Operator SHALL be able to add new validators to their megapool
- A Node Operator MAY be able to migrate existing validators to their megapool
     - Note that this would involve updating the validators' BLS withdrawal
       credentials to the megapool address
- New and existing validators' withdrawal credentials MUST be checked to
  correctly refer to the megapool address, with the validator status set to
  Dissolved if the check fails
     - The check MAY occur after the validator is added initially as Prestaked
- A Node Operator SHALL be able to remove exited validators from their megapool

### Funds Management

- This RPIP assumes the continued use of separate 32 Ether validators. Support for
  EIP-7251 'Max EB' validators will require additional specification and development.
- The megapool SHALL track the total capital ownership of the validator(s) between
  the node operator and the rETH stakers.
- The megapool MUST distinguish between capital funds present in the contract and
  rewards funds at all times.
- A Node Operator SHALL be able to distribute rewards from all validators in their
  megapool at once.
- Rewards distribution MAY be temporarily blocked while validators are exiting or
  pending removal.
- There SHALL be a reward distribution function in the megapool
  - When called, rewards from the node operator's bonded ETH SHALL be held in
    the megapool as unclaimed node operator funds.
  - When called, `node_operator_commission_share` of rewards SHALL be held in 
      the megapool as unclaimed node operator funds.
  - When called, `reth_share` of rewards SHALL be sent to the rETH contract
  - When called, `voter_share` of rewards SHALL be sent to a merkle rewards
    distributor contract
  - When called, `surplus_share` of rewards SHALL be sent to the appropriate
    surplus disposition contract
  - This function SHALL allow any user to call it
  - If called by the node operator, this function SHOULD claim all unclaimed
    node operator funds
- There SHALL be a capital distribution function in the megapool
  - When called, capital from the node operator's bonded ETH that has been
    released from exited validators SHALL be held in the megapool as unclaimed
    node operator funds.
  - When called, capital borrowed from the protocol that has been released from
    exited validators SHALL be sent to the rETH contract.
  - This function SHALL allow any user to call it following a mandatory time
    delay configurable by the pDAO. The delay SHALL be initialized by a
    `startUserDistribute` function. After the delay is complete, any user may
    then call the capital distribution fuction.
  - If called by the node operator
    - This function SHOULD claim all unclaimed node operator funds
    - This function SHALL be immediately callable without delay
- There SHALL be a function to claim unclaimed node operator funds; when called
  - If unclaimed node operator funds >= penalties
    - unclaimed node operator funds SHALL be decreased by penalties
    - penalties SHALL be set to 0
  - If unclaimed node operator funds < penalties
    - unclaimed node operator funds SHALL be set to 0
    - penalties SHALL be decreased by unclaimed node operator funds
  - Unclaimed node operator funds SHALL be transferred to the node operator's
    withdrawal address
  - Unclaimed node operator funds SHALL be set to 0
- A Node Operator SHALL be able to withdraw unclaimed node operator funds to
  their withdrawal address
- A Node Operator MAY be able to use unclaimed node operator funds for redeposit
  to the beacon chain.
- In the event the rETH proportion of the withdrawn capital is insufficient
  to fully repay the protocol borrowed capital, the shortfall SHALL be repaid
  from the node operator's capital
   - If the node operator's capital is insufficient to repay the shortfall, a
     deficit balance of the shortfall SHALL be recorded
- If there is a deficit balance > 0:
   - Any future node operator rewards or capital distributions SHALL be deducted
     from until the deficit is fully repaid.
   - There SHALL be a function that allows anyone to directly repay a deficit.
   - Deficit repayments SHALL be transferred to the rETH contract
- Newly deposited capital that is awaiting deposit to the beacon chain SHALL be
  excluded from capital distribution, but rather be subject to separate functions
  for `prestake` and `stake` transactions.
- The megapool MAY be used as the node operator's authorized fee recipient for
  execution layer rewards if they are not opted into the smoothing pool. These
  funds shall be subject to rewards distribution.

### RPL Staking

This RPIP includes a rework of the treatment of RPL staked on Rocket Pool
nodes. We refer to RPL staked for a node prior to the implementation of this
RPIP as "legacy staked RPL", and to RPL staked according to the rules described
in this RPIP as "megapool staked RPL".

Megapool staked RPL (in the protocol, ie, before being withdrawn) can be in one
of two states:
- Staked: the RPL has been deposited and staked (locked) in the protocol, for a
          node with a megapool deployed
- Unstaking: the RPL has been marked as intended to be withdrawn

Rules specifying the movement of staked RPL are as follows:

- Legacy RPL staking (ie, increasing a node's legacy staked RPL balance) SHALL
  be disabled
- There is no staked RPL requirement for adding validators to a megapool
- A Node Operator MAY change the state of any amount of megapool staked RPL
  associated with their node from Staked to Unstaking at any time
    - The time at which Staked RPL was last changed to Unstaking RPL is called the
      "last unstake time" for the node
- Only Unstaking RPL can be withdrawn from the protocol, and only if the last
  unstake time for the node is at least `unstaking_period` (this parameter is
  defined in [RPIP-30](RPIP-30.md))
    - This does not have any impact on the extant rules governing the
      withdrawal of legacy staked RPL
- If any Unstaking RPL is withdrawn for a node, all Unstaking RPL for that node
  MUST be withdrawn at once
- If the last unstake time is at least `unstaking_period` when additional
  megapool staked RPL is having its state changed to Unstaking, the RPL already
  in the Unstaking state SHOULD be withdrawn first

### Vote Eligibility

- All nodes are eligible to receive vote power
- For nodes eligible to receive vote power, the sum of legacy staked RPL and
  megapool staked RPL in the Staked state SHALL contribute to vote power up to
  150% of the value of total bonded ETH (from both megapools and legacy
  minipools)
  - Note that Unstaking RPL SHALL NOT be counted
  - If this RPIP is ratified, a note SHALL be added to the "Eligibility" section of [RPIP-4](./RPIP-4.md) saying that it has been superseded by this section

## Reference Implementation
See <https://github.com/rocket-pool/rocketpool-research/blob/master/Megapools/megapools.md>

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
