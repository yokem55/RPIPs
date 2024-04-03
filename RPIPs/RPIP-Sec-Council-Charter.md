---
rpip: TBD
title: PDAO Security Council Charter
description: Establishment of qualifications, selection process and duties of the PDAO Security Council 
author: Yokem (@yokem55)
discussions-to: https://discord.com/channels/405159462932971535/774497904559783947/1224766553267109960
status: Draft
type: Meta
created: 2024-04-03
requires: RPIP-33
---

## Abstract
This RPIP is an attempt to shape the qualifications, duties, and expectations of security council members, as well as establish processes for their election, re-election or removal, and liveliness monitoring. 

## Motivation
RPIP-33 establishes an on-chain governance mechanism for setting and alterting key protocol settings and variables. Most of those settings do not need to be changed in a time-sensivitve manner and can wait for a normal PDAO governance process to complete. However, to still allow rapid settings changes that might be needed in the event of a security incident so as to protect the protocol from (further) harm, RPIP-33 envisions allowing a "Security Council" to propose and vote to exececute a subset of the protocol settings in an immediate manner. However the selection and implementation of that Security council was intentionally left for future develoment.

Per RPIP-33 (Implementation of an On-Chain pDAO):
> Security Council membership is a serious role and the pDAO SHOULD develop strong entry requirements and processes for routinely flushing stale members. The development of these requirements and processes is left for a future RPIP. To begin with, the current pDAO guardian SHALL be the sole member.

This RPIP seeks to fulfill the development of those requirements and processes.

## Specification
### Qualifications for prospective members
Security council membership is a high commitment, high responsibility role, and should not be pursued lightly.

**Minimum Qualifications:**
- Have a strong background in EVM blockchain security, safety practices and procedures.
- Ability to ask critical questions of a technical nature to better understand the underlying mechanics of blockchain transactions and events.
- Thorough knowledge of the the normal behaviors of the Rocket Pool protocol.
- Familiarity with the the PDAO settings available to the Security Council for immediate execution, their intended behaviors, the potential circumstances for when they might be need to be changed and the implications for their usage.

**Reccomended Qualifications:**
- Have the ability to read, interpret and understand solidity smart contract code at a level sufficient to understand the author's intentions in writing it and the expected behaviors of the code. 
- Have thorough knowledge of the Rocket Pool smart contracts, their organization, relationships, functions, and expected behaviors under nominal operation.
- Have experience with transaction tracing and interpretation with tools such as Tenderly and other block explorers.
- A long term presence and history in the Rocket Pool community.

**Mandatory Disclosures**
During the identity and alignement statement phases of an election, prospective members must disclose the following:
- Their identity status (one of):
  - Fully pseudonymous
  - Privately doxed to one or more publicly designated community member(s)
  - Fully and publicly doxed
- What legal jurisdiction(s) they are subject to.

### Member duty expectations
- Members shall be available and able to respond in an "on call" manner, on a 24/7 basis, within `[a short period of time - an hour or two?]` of notification of a security incident necessitating security council action.
- Members shall provide to the other members of the council and the core development team direct telephone number(s) that they can be reached at for notification of a security incident.
- The security council shall not be a general policy making body. Its role is restricted to making appropriate protocol setting changes in response to  security incidents. Members are strongly encouraged to maintain a clear separation between their security council roles and any other PDAO policy advocacy they may engage in.
  
### Council duty expectations
- The security council shall, no less then twice per year, perform drills to execute emergency protocol settings changes on a private testnet deployment of Rocket Pool. A public report describing the drill and its outcome shall be published by the council.
- The security council shall publish postmortem reports in a timely manner regarding security incidents necessitating a protocol setting change, with as much detail as can be revealed without further security compromise.
- On a roughly a monthly basis at an irregular and generally unpredictable interval, any member may begin a "Proof of keys" check by publicly posting a signed message including a recent mainnet Ethereum finalized blockhash in the message.  Within one day, all other members should also post a similar message. Should a member fail this liveliness check, the pDAO may move to vote to remove the member.

### Size and composition
- The security council shall be composed of not more than 10 members. Should the membership fall to 7 or fewer members via removal or resignation, the PDAO shall vote on invitations to complete the membership within 30 days.
- Not more than 4 members shall be from the core development team or members of the ODAO, excluding the non-operator members of the "Rocket Scientists" ODAO seat.

### Elections and terms
Elections for the Security Council shall be conducted in two phases. The first phase establishes a roster of candidates equal to the number of vacancies (`n`) via an off chain election similar to management committee elections, complete with the nomination and identity and alignment statement process. The second phase then takes the top `n` vote winners from the first phase who are then proposed for invitation in an on-chain vote. Should not all `n` prospective members win an affirmative invitation in the on-chain PDAO vote, the process shall be repeated until the full membership is established. PDAO security council invitation proposals that did not go through the off-chain election process should be vetoed in most circumstances. 

The initial full membership shall be placed into two groups by random selection, identified as group A and group B. Thereafter, in six month intervals, each group shall be subject to an off-chain vote. Should the top five vote winners in the off-chain vote include prospective members not currently on the council, an on chain removal vote for the non-returning members shall proceed at the same time as invitation votes for the newly elected members. For example, after the first complete election, in 6 months group A is put up to a vote, in 12 months group B is put up to a vote, and in 18 months, group A is put up for a vote again. This process ensures no more than half of the council is "up for re-election" at any given time, while ensuring that the membership has not grown stale, and minimizing the time which the council has a smaller membership then is expected. Should a member decide to not seek reelection while still completing their term, they shall time their resignation and 'leave' transactions to align with the on-chain votes for the replacement member.

### Vacancies and Removals

### Planned Absences
Should a member wish to be unavailable in a planned manner (ie, vacation), they may propose up to two weeks of planned absence to the rest of the council, up to twice per year. The council may approve the absence with an informal majority vote. If approved by the council, the member taking an absence shall privately share with the council a signed message with a recent finalized mainnet blockhash along with their expected dates of for leaving and returning. Should a liveliness check as described above occur during the member's absence, the council may publish the signed absence message in lieu of the member publishing an actual timely one. At no time shall more members be on temporary absence then would prevent a quorum + one of available mmembers making a settings change. For example, with 10 members on the council and the default quorum (51% or 6 votes), no more then 3 members may be on planned absence a the same time. 

Members should strive to avoid a quorum denying number of members being present in the same physical location. For examply if there are 10 members on the council, and a quorum of 6, no more then 4 members should be at the same physical location (i.e. a conference). Coordination of events with common attendance should planned accordingly. 

### Compensation
Security council membership requires a high level of availability and responsibility for quick action. But the role does not require much ongoing work. As such, compensation for "availability" is needed, but given the limited ongoing time requirements, the compensation does not need to be large. An initial amount equivalent to $[xxx] per 28-day rewards period, per member is suggested here. This corresponds to roughly $[xx] per day of on-call availability. To keep the management of compensation simple, planned absences shall not be deducted from the compensation. 

## Rationale
[To fill in]

## Backwards Compatibility
N/A

## Test Cases
N/A

## Reference Implementation
N/A

## Security Considerations
[To fill in]

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).



