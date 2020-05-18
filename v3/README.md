# YALLAND V3 Spec

## Contract list

### Both Chains:
* [`YALLRegistry`](./YALLRegistry.md) (both Registry + ACL in one contract);
* [`YALLGovernance`](./YALLGovernance.md) - proposal management system, has ownerhsip/proxy ownership permissions for all related contracts on the chain;

### Home Chain (xDai):
* [`YALLToken`](./YALLToken.md) - ERC20 token;
* [`YALLDistributor`](./YALLDistributor.md) - Member accounting, emission distribution;
* [`YALLExchange`](./YALLExchange.md) - smart contract for YALL exchange between program participants and non-profit organizations;
* `YALLVerification` - Verifier add/removal by `YALLGovernance`, member add/removal actions by a verifier;
* [`YALLEmissionRewardPool`](./YALLEmissionRewardPool.md) - Emission distribution among verifiers and delegates;
* [`YALLCommissionRewardPool`](./YALLCommissionRewardPool.md) - A system contracts commission, withdrawn and distributed among verifiers, delegates and (optionally) members;
* `YALLTokenHomeMediator` - Bridges `YALLToken` with `YALLTokenEthereum`;
* [`YALLStakingHomeMediator`](./YALLStakingHomeMediator.md) - A mediator with synced information from `YALLStakingForeignMediator`;

### Foreign Chain (Ethereum):
* [`YALLTokenEthereum`](./YALLTokenEthereum.md) - An ERC20 token bridged with `YALLToken` on xDai chain;
* [`YSTToken`](./YSTToken.md) - An ERC20 governance token for staking;
* `YALLTokenForeignMediator` - Bridges `YALLTokenEthereum` with `YALLToken`;
* [`YALLStakingForeignMediator`](./YALLStakingForeignMediator.md) - Checkpointable Staking & Foreign Mediator in-one contract;

--------
## Launch Stages
### Stage #1 (slated to May 24th 2020):

![Stage #1 Deployment](../images/deployment-stage-1.svg)

#### xDai chain

* `YALLRegistry`
* `YALLToken`
* `YALLDistributor`
* `YALLExchange`
* `YALLTokenHomeMediator`

###### xDai ACL Roles
* `YALL_TOKEN_MINTER` - `YALDistributor` contract
* `YALL_TOKEN_BURNER` - `YALDistributor` contract
* `YALL_TOKEN_WHITELIST_MANAGER` - external address
* `EXCHANGE_FUND_MANAGER` - external address
* `EXCHANGE_OPERATOR` - external address
* `EXCHANGE_SUPER_OPERATOR` - external address
* `FEE_MANAGER` - external address
* `PAUSER` - external address
* `VERIFIER` - external address

#### Ethereum Mainnet

* `YALLTokenEthereum`
* `YALLTokenForeignMediator`

###### Ethereum Mainnet Permissions
* `YALLTokenEthereum` `owner` - external address
* `YALLTokenForeignMediator` `owner` - external address
* `YALLTokenForeignMediator` `proxy owner` - external address

### Stage #2:
![Stage #2 Deployment](../images/deployment-stage-2.svg)

TBD...

--------
## Development Stages
✅ - Ready

⚠️ - Minimal viable version + behaviour tests are ready, unit tests pending

❌ - Pending

### Stage #1:

* ✅`YALLRegistry`
* ✅`YALLToken`
* ✅`YALLDistributor`
* ✅`YALLExchange`

### Stage #2:

* ⚠️`YST`
* ⚠️`YALLStakingForeignMediator`
* ⚠️`YALLStakingHomeMediator`
* ⚠️`YALLEmissionRewardPool`
* ⚠️`YALLCommissionRewardPool`
* ⚠️`YALLGovernance`

### Stage #3:

* ❌`YALLVerification`
