# YALLAND V3 Spec

## Contract list

## Both Chains:
* [`YALLRegistry`](./YALLRegistry.md) (both Registry + ACL in one contract)
* `YALLGovernance` - proposal management system, has ownerhsip/proxy ownership permissions for all related contracst on the chain

Home Chain (xDai):
* [`YALLToken`](./YALLToken.md) - ERC20 token
* [`YALLDistributor`](./YALLDistributor.md) - Member accounting, emission distribution
* [`YALLExchange`](./YALLExchange.md) - `YALLToken` exchange
* `YALLVerification` - Verifier add/removal by `YALLGovernance`, member add/removal actions by a verifier
* [`YALLEmissionRewardPool`](./YALLEmissionRewardPool) - Emission distribution among verifiers and delegates
* [`YALLCommissionRewardPool`](./YALLCommissionRewardPool.md) - A system contracts commission, withdrawn and distributed among verifiers, delegates and (optionally) members
* `YALLTokenHomeMediator` - Bridges `YALLToken` with `YALLTokenEthereum`
* `YALLStakingHomeMediator` - A mediator with synced information from `YALLStakingForeignMediator`

Foreign Chain (Ethereum):
* [`YALLTokenEthereum`](./YALLTokenEthereum.md) - a ERC20 token bridged with `YALLToken` on xDai chain
* [`YSTToken`](./YSTToken.md) - a ERC20 governance token for staking
* `YALLTokenForeignMediator` - Bridges `YALLTokenEthereum` with `YALLToken`
* `YALLStakingForeignMediator` - Checkpointable Staking & Foreign Mediator in-one contract

--------
## Launch Stages
### Stage #1:
#### xDai chain(only)

* `YALLRegistry`
* `YALLToken`
* `YALLDistributor`
* `YALLExchange`

### Stage #2:

TBD...

--------
## Development Stages
### Stage #1:

* `YALLRegistry`
* `YALLToken`
* `YALLDistributor`
* `YALLExchange`

### Stage #2:

* `YST`
* `YALLStakingForeignMediator`
* `YALLStakingHomeMediator`

### Stage #3:

* `YALLVerification`
* `YALLEmissionRewardPool`
* `YALLCommissionRewardPool`

### Stage #4:

* `YALLGovernance`
