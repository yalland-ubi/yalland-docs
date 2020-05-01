# YALLCommissionRewardPool

Claims commission from registered contracts once a period. Distributes the claimed commission among verifiers, delegators and (optionally) members. The funds weren't claimed by any of the validators or members will be redistributed to the next period. This rule doesn't apply to delegator rewards.

## Detailed agorithm

This example explains who can claim commission, for what period, and how it is calculated.

#### Period 1:

* verifiers Alice, Bob, Charlie are connected before period started;
* in period 1, the Alice verifier becomes inactive after connection;
* in the interval between calling the transition function of the period 0-> 1 and the time of changing the period 1-> 2, a commission of 20 YAL was accumulated in ERC20 contract.

#### Period 2:

* at the beginning of the period only Bob and Charlie verifiers are active;
* in the interval between the time of changing the period 1-> 2 and the call to the transition function of the period 1-> 2, the commission 3 YAL was accumulated in ERC20 contract;
* the transition function of the period 1-> 2 is called up, a commission of 23 YAL is withdrawn from the ERC20 contract accumulated between the call of the transition function of the period 0-> 1 and the call of the transition function of the period 1-> 2, the extended commission is assigned for payment in the current period 2;
* Charlie verifier is deactivated;
* Alice was not active during the transition period of the period 1-> 2 and is not active now, she will not participate in the distribution of the commission in the period 2;
* Bob was active on the date of the transition of the period 1-> 2 and is active now, so he participates in the distribution of the commission in the period 2;
* Charlie was active during the transition period of the period 1-> 2 but was not active now, he did not manage to withdraw the commission in period 2 while he was active, and now he can’t extend it, but if it is activated again in period 2, he will be able to do it (slot on Charlie was assigned because it was active for the transition 1-> 2).

## Permissions
The contract does not implement Ownable trait, so there is no owner.

## Interface

* Permissionless
  * [#triggerTransition()](#triggerTransition)
* COMMISSION_MANAGER ACL Role
  * [#setShares()](#setShares)
  * [#setCommissionSources()](#setCommissionSources)
* Delegator
  * [#claimDelegatorReward()](#claimDelegatorReward)
* Verifier
  * [#claimVerifierReward()](#claimVerifierReward)
* Member
  * [#claimMemberReward()](#claimMemberReward)


### Permissionless methods

##### #triggerTransition()

The method performs the following actions:

* Withdraws all earned commission in YALLs from registered list of contracts
* Redistributes funds not claimed by verifiers and members at the perevious period to the current one
* Calculates and stores reward values for delegators, verifiers and members

Information about the current period id and it's beginning resides in `YALLDistributor` contract.

The commission is not pegged to a particular period ID, so commission earned by the contracts in period N could be withdrawn along with part of the commission earned in the period between the beginning of period N + 1 and the method call.

###### Delegator reward calculation

A reward of delegator depends on his stake in YST tokens, it's not the same for all delegators.

```
Rd = Ct * Ds * (SpN / TpN)

Rd - a reward for a particular delegator
Ct - a total withdrawn commission from all contracts, in YALL
Ds - delegators share of total commission, in %
SpN - a delegator stake at the timestamp of the beginning of period N, in YST
TpN - total stakes of all delegators at the timestamp of the beginning of period N, in YST
```

Information about a delegator stake and total stakes of all delegators resides at `YALLStakingHomeMediator` contract.


###### Verifier reward calculation

Verifier reward is the same for all verifiers.

```
Rv = Ct * Vs / Tv

Rv - a reward for a verifier
Ct - a total withdrawn commission from all contracts, in YALL
Vs - verifiers share of total commission, in %
Tv - a total amount of active verifiers at the time of calling this method
```

Information about active verifiers resides in YALLVerification contract. In order to make active verifiers count at the time of calling this method equal verifiers count at the beginning of the given period, the verifiers contract should prevent actions that modify this count until this method be executed.

###### Member reward calculation

Member reward is the same for all members.

```
Rm = Ct * Ms / Tm

Rm - a reward for a verifier
Ct - a total withdrawn commission from all contracts, in YALL
Ms - members share of total commission, in %
Tm - a total amount of active members at the time of calling this method
```

Information about active members resides in YALLDistributor contract. In order to make active member count at the time of calling this method equal member count at the beginning of the given period, YALLDistributor contract should prevent actions that modify this count until this method be executed.

### COMMISSION_MANAGER ACL Role

COMMISSION_MANAGER can call the following methods:

##### #setShares()

It sets shares for `validators`, `delegators`, and `members`. The sum of these values should be equal to 100%. Group can be disabled by setting its share to 0.

##### #setCommissionSources()

It sets a new list of contracts, `#triggerTransition()` will use this list later to fetch commission from them.

### Delegator interface

##### #claimDelegatorReward()

A delegator who had an active stake at the beginning of the given period can claim his reward. The reward can be claimed even during one of the further periods.

### Verifier interface

##### #claimVerifierReward()
A verifier who is eligible at the moment can claim his reward. Eligibility is defined using the same logic as eligibility of a member to claim emission reward in the current period.

### Member interface

##### #claimMemberReward()
A member who is eligible at the moment can claim his reward. Eligibility is defined using the same logic as eligibility of a member to claim emission reward in the current period.
