# YALLStakingForeignMediator

The contract has two distinct groups of functionality - a balance caching per timestamp and a mediator logic.

Balance caching is done in using the approach introduced in MiniMe token with several adjustments.
One of the adjustments is ignoring parent tokens and balance inheritance.
Another one is caching not per block but per timestamp.

Although [poanetwork/tokenbridge-contracts](https://github.com/poanetwork/tokenbridge-contracts/tree/a5946e7024caf598e562da916675a3b269ab293d/contracts/upgradeable_contracts) repo has audited versions of bridges, they were ignored due to several reasons:

* Solidity version is 0.4.x while Yalland contracts use Solidity v0.5.x;
* YALLStakingForeignMediator has one direction of messages, it doesn't accept incoming messages from Home bridge;
* YALLStakingForeignMediator message logic doesn't replicate any of audited bridges logic;

## Inheritance

* TimestampCheckpointable - balance per timestamp caching;
* BasicMediator - custom implementation of mediator logic;

## Use Cases

To become a delegator, a YST token holder should stake them at `YALLStakingForeignMediator`.
Like other ERC20 deposit functions, this is done using 2 separate transactions: approve and transferFrom wrapped in the contract method call.

When `YALLStakingForeignMediator` receives the deposit it caches its value by the current block timestamp. Along with caching, it transfers the given timestamp and balance to `YALLStakingHomeMediator` home mediator. Particularly it calls `#setBalance(address _delegator, uint256 _timestamp, uint256 _balance)`.

Since being deposited, the cached values can be used by:

* YALLGovernance contract, by calculating the weight of a delegator vote;
* YALLEmissionRewardPool, by calculating a delegator reward amount proportional to the stake he has;
* YALLCommissionRewardPool, by calculating a delegator reward amount proportional to the stake he has;

## Stake locking

* A delegate can lock a part or entier stake if he want to become a verifier.
* The locked stake serves as a deposit to protect protocol from the delegate malicious behaviour.
* The locked stake can be slashed by a `locked_stake_slasher` address set by a governance contract.
* When the locked stake changes, a bridge sends a correspondive message with an updated value from mainnet to xDai

## Bridge interface 

## Interface


* [Owner](#owner-interface)
  * [#setYallTokenContract()](#setyalltokencontractaddress-_newyalltokenaddress)
  * [#setMediatorContractOnOtherSide()](#setmediatorcontractonothersideaddress-_newmediatorcontractonotherside)
  * [#setRequestGasLimit()](#setrequestgaslimituint256-_newrequestgaslimit)
  * [#setCoolDownPeriodLength()](#setcooldownperiodlengthuint256-_newcooldownperiodlength)
* [YST Holder](yst-holder-interface)
  * [#stake()](#stakeuint256-_amount)
  * [#unstake()](#unstakeuint256-_amount)
  * [#releaseCoolDownBox()](#releasecooldownboxuint256-_cooldownboxid)
* [Permissionless](#permissionless-interface)
  * [#triggerTransition()](#synccachedbalanceaddress-_delegator-uint256-_timestamp)
  
### Owner Interface
##### #setYallTokenContract(address _newYallTokenAddress)

Sets a new yallTokenAddress by its address

##### #setMediatorContractOnOtherSide(address _newMediatorContractOnOtherSide)

Sets a mediator contract on another side by its address

##### #setRequestGasLimit(uint256 _newRequestGasLimit)

Sets a request gas limit in weis

##### #setCoolDownPeriodLength(uint256 _newCoolDownPeriodLength)

Sets a new coolDownPeriodLength in seconds. The change won't affect the existings boxes.

##### #slashLockedStake(address _delegate, uint256 _amount)

The governance contract can slash any locked stake. Decrements both staked value and locked stake value. Creates a new cooldownbox with a beneficiary assigned to the governance contract.

### YST Holder Interface

##### #stake(uint256 _amount)

Stakes a given amount of YST tokens. Caches the balance by the current block timestamp key. Transfer a message using AMB to the home chain.

##### #unstake(uint256 _amount)

Unstakes a given amount of YST tokens. An important thing to notice is that it doesn't transfer the unstaked YST balance immediately. Instead, it deducts the given amount in the cache, notifies the mediator on the other side about the amount and creates a personal CoolDownBox record with the following information:

```
CoolDownBox {
	id: uint256 - unique id of the box, increments each new box by 1 starting from 1
	holder: address - a member who is eligibly releasing this box;
	amount: uint256 - amount to release
	releaseAt: uint256 - timestamp specifying since when this box can be released by a holder
}
```

A delegate can unlock his stake with exclusion of the locked value. For ex. if Alice has 30K stake and 20K of this stake is locked, she could unstake only 10K.
If she wants to unstake the entire stake of 30K, she should unlock it first, and only after unlocking she would be able unstaking the entire 30K stake.

##### #lock(uint256 _amount)

A delegate locks his stake

* Requires that the current staked balance is greater than.
* When locked, the current stake is not decremented.
* The Foreign bridge notifies the Home bridge about locked balance update.

##### #releaseCoolDownBox(uint256 _coolDownBoxId)

A delegator releases a cooldown box if he is eligible to do that.

### Permissionless Interface

##### #syncCachedBalance(address _delegator, uint256 _timestamp)

The AMB sync process in this bridge is not bidirectional but has one direction from `Foreign` to `Home` networks. In order to save this simplicity of the protocol, this fix message pattern used by most AMB bridges to fix the failed transactions was ignored. The only type of transaction that could fail is `#setBalance(address _delegator, uint256 _timestamp, uint256 _balance)` method call. Since the value sent to the `Home` bridge can't be changed later, the most convenient way to fix a failed transaction is to send this value again. So this method allows anyone to sync an already existing cache record in this contract.

##### #syncLockedBalance(address _delegator)

Sends AMB message to `Home` chain with the current locked balance of a delegator.
