# YALLEmissionRewardPool

Claims the emission reward from YALLDistributor contracts once a period.
Distributes the claimed reward among verifiers and delegators. The distributed rewards are minted on an explicit request of each particular verifier or delegator.

Verifiers can claim their rewards only for the current period. If a validator doesn't claim a reward in the given period, he won't be able to do this later.

Delegators have no period restrictions for claiming rewards. They can claim a reward for the given period at any time in the future.

## Inheritance

## Inbound ACL Roles

* `EMISSION_MANAGER`

## Permissions

The contract does not implement Ownable trait, so there is no owner.

## Interface

* Permissionless
  * [#triggerTransition()](#triggerTransition)
* EMISSION_MANAGER ACL Role
  * [#setShares()](#setShares)
* Delegator
  * [#claimDelegatorReward()](#claimDelegatorReward)
* Verifier
  * [#claimVerifierReward()](#claimVerifierReward)

### Permissionless methods

##### #triggerTransition()

The method performs the following actions:

* Withdraws all earned reward in YALLs from YALLDistributor
* Redistributes funds not claimed by verifiers at the previous period to the current one
* Calculates and stores reward values for delegators and verifiers

Information about the current period id and it's beginning resides in `YALLDistributor` contract.

The methods executes `YALLDistributor->#triggerTransition()` function, if it has't been executed yet for the current period.

###### Delegator reward calculation

A reward of a delegator depens on his stake in YST tokens, it's not the same for all delegators.

```
Rd = Et * Ds * (SpN / TpN)

Rd - a reward for a particular delegator
Ct - total withdrawn emission for given period, in YALL
Ds - delegators share of total emission, in %
SpN - a delegator stake at the timestamp of beginning of period N, in YST
TpN - total stakes of all delegators at the timestamp of beginning of period N, in YST
```

Information about a delegator stake and total stakes of all delegators resides at `YALLStakingHomeMediator` contract.


###### Verifier reward calculation

Verifier reward is the same for all verifiers.

```
Rv = Et * Vs / Tv

Rv - a reward for a verifier
Et - total withdrawn emission from all contracts, in YST
Vs - verifiers share of total emission, in %
Tv - total amount of active verifiers at the time of calling this method
```

Information about active verifiers resides in YALLVerification contract. In order to make active verifiers count at the time of calling this method equal verifiers count at the biginning of the given period, verifiers contract should prevent actions that modify this count until this method be executed.

### EMISSION_MANAGER ACL Role

EMISSION_MANAGER can call the following methods:

##### #setShares()

It sets shares for `validators` and `delegators`. The sum of these values should be equal to 100%. A group can be disabled by setting it's share to 0.

### Delegator interface

##### #claimDelegatorReward()

A delegator who had an active stake at beginning of the given period can claim his reward. The reward can be claimed even during one of further periods.

### Verifier interface

##### #claimVerifierReward()
A verifier who is eligible at the moment can claim his reward. Eligibility is defined using the same logic as eligibility of a member to claim emission reward in the current period.

