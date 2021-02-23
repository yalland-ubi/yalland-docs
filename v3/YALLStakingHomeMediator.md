# YALLStakingHomeMediator

The contract can be changed only by `YALLStakingForeignMediator` at the opposite foreign chain. It replicates cache system from `YALLStakingForeignMediator` and exposes
this information for local contracts such as governance and reward distribution pools

## Interface

* [Owner Interface](#owner-interface)
  * [#setMediatorContractOnOtherSide()](#setmediatorcontractonothersideaddress-_newmediatorcontractonotherside)
* [AMB Interface](#amb-interface)
  * [#setCachedBalance(address _delegator, uint256 _timestamp, uint256 _balance)](#stakeuint256-_amount)

### Owner Interface

##### #setMediatorContractOnOtherSide(address _newMediatorContractOnOtherSide)

Sets a mediator contract on another side by its address

### AMB Interface

##### #setCachedBalance(address _delegator, uint256 _timestamp, uint256 _balance)

AMB updates a cached record with the given data. `_timestamp` value should be greater than the last one in the cache, a transaction will revert otherwise.

This could happen in the following case:
- AMB updates value updated by a stake deposit, but the transaction fails due to some reason
- AMB updates value updated by a stake deposit again, the transaction is successful
- Someone tries manually update the first transaction balance using `YALLStakingForeignMediator->syncCachedBalance()`

Although the foreign mediator store both balance changes, the home mediator will store only the last change. The first transaction update will be lost permanently and won't be accounted for by contracts like governance or reward distribution pools.

##### #setLockedBalance(address _delogator, uint256 _balance)

AMB updates a current locked balance value. The is no need for caching it.
