# YALLDistributor

## Important Updates
### 14/03/2020
- Use lastActivateTimestamp and lastDeActivateTimestamp in structMember and activateMember(), deactivateMember(), claimFunds() functions instead of mapping (uint256 //period// => bool //deactivationStatus//)

### 13/03/2020
-  add mapping (uint256 //period// => bool //deactivationStatus//) public deactivationPerPeriod;
- in deactivateMember() add deactivationPerPeriod.[getCurrentPeriod()] = true;
- check in claimFunds() activationPerPeriod[getCurrentPeriod()-1] == false; 

## Constructor arguments (the values can't be changed later):

The values that can't be changed later:
- YALLRegistry contract address
- periodLength - in seconds
- genesisTimestamp - timestamp of the beginning of the first period

The values that can be changed later:
- periodVolume - the number of tokens to mint during a single period
- emissionPoolRewardShare

## Contract Permissions
### Outbound

The contract has `YALL_MINTER` and `YALL_BURNER` ACL roles assigned, hence this allows it minting and burning YALL_TOKENS.

### Inbound

|Role name| Allowed methods |
|---|---|
|`DISTRIBUTOR_MANAGER`| `setEmissionPoolRewardShare()`, `setPeriodVolume()`|
|`DISTRIBUTOR_VERIFIER`| `addMembersBeforeGenesis()`, `addMembers()`, `addMember()`, `enableMembers()`, `disableMembers()`, `changeMemberAddresses()`, `changeMemberAddress()`|
|`DISTRIBUTOR_EMISSION_CLAIMER`| `claimEmissionPoolReward()`|
|`FEE_MANAGER`| `setGsnFee()`|
|`FEE_CLAIMER`| `withdrawYALLs()`|

## Reward calculations

* Any member which is inside the member list and is active at the moment can claim distributed tokens if he was already included at the beginning of the current period
* When a new user joins as a member, he should wait when the next period starts in order to start receiving funds
* An already existing member could claim his funds for a period if he had not claimed it yet
* If a member had been deactivated at beginning of the current period, and later he had been activated again, he could claim his reward
* If a member had been deactivated in a current period and had not claimed funds yet, he can't claim the funds unless he will be activated in this period again
* If a member claims his reward in the current period, a verifier performs activate/deactivate actions on the member in the current period, the member can't claim the period reward again since he already did.
* There is no way to delete member completely, only to deactivate him
* Member can't claim funds for a period other than the current
* A member mints his reward on each claim, the reward amount is calculated once within `handlePeriodTransitionIfRequired` modifier
* The funds not claimed by users are not minted, so there is no need to redistribute them

## Dealing with period transition

In order to detect a new period and perform corresponding actions, there is `handlePeriodTransitionIfRequired` modifier introduced. If there is a new period started, the modifier will:
* calculate and cache `rewardPerUser` for the current period.
* calculate and cache `verifierReward` for the current period.

The `activeMemberCount` variable at this time should have exactly the same value as it had at the particular time when the period had started.

The modifier is included into the following methods:

* addMember()
* addMembers()
* activateMember()
* deactivateMember()
* claimFunds()

So each call for the aforementioned methods would cause these checks.

## Can claim a reward constraints

To calculate whether a member can claim a reward in this period or not, the following constraints are imposed:

```
// this also implies that Le <= Ld
assert(memberIsActiveNow == true)

if (Ld != 0 && CPid != 0) {
  assert(
      // a member has been both disabled and enabled in the current period
      (Ld >= CPb && Le >= CPb)
      // a member was both disabled and enabled in the previous period
      || (Ld >= PPb && Le >= PPb && Ld < CPb && Le < CPb)
      // a member was both disabled and enabled before the current period started
      || (Ld < CPb && Le < CPb)
  )
}

// Can claim a reward
Ok()

----
CPid - current period ID
CPb - Current period begins at timestamp
PPb - Previous period begins at timestamp
Le - Member last enabled at
Ld - Member last disabled at
```

The code above checks for the major requirement for a reward claiming, ensuring that a member:

* is active now
* was active at the timestamp when the current period started

## Other
* Contract implements Pausable and Ownable traits from OZ
* Contract uses SafeMath from OZ for uint256 values
* Contract allows the owner claiming any amount ETHs, ERC721 tokens and all ERC20 tokens other than the one specified as `token` storage variable
* The contract doesn't need a `burner` role as DCity contract does.

