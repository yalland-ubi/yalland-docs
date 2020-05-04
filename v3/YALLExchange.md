# YALLExchange

## GSN support

GSN support is implemented for the following methods:

* `createOrder()` - a program member creates an exchange order

## Inbound ACL Permissions

|Role name| Allowed methods |
|---|---|
|`EXCHANGE_MANAGER`| `YALLExchange->setDefaultExchangeRate()`, `setCustomExchangeRate()`, `setTotalPeriodLimit()`, `setDefaultMemberPeriodLimit()`, `setCustomPeriodLimit()`|
|`EXCHANGE_OPERATOR`| `closeOrder()`, `cancelOrder()`|
|`EXCHANGE_SUPER_OPERATOR`| `void()`|
|`FEE_MANAGER`| `setGsnFee()`|
|`FEE_CLAIMER`| `withdrawFee()`|
|`PAUSER`| `pause()`, `unpause()`|

### YALL  active member

Any active YALL program member can call:

- `#createOrder()`

## Accumulators
There are 4 accumulators in this exchange contract:

* TotalExchangedYal
* YalExchangedByPeriod
* MemberTotalExchangedYal
* MemberYalExchangedByPeriod

Parameters are incremented on: `#createOrder()`

and decremented on: `#cancelOrder()`, `#voidOrder()`

The order creation period ID is used as reference for the accumulator.

## Exchange limits

All these limits are checked once on `createOrder` method call.

### Limit #1. Personal exchanged volume limit

A member limit in YAL tokens are calculated by the following formula:

```
Am = Ct - Et + Vt

Am - max. amount available to exchange
Ct - total claimed amount by member in all periods
Et - total exchanged amount by member in all periods
Vt - total voided amount
```

### Limit #2. Member period limit.

Member period limit should satisfy the following requirement:

```
L = Lm || La

if (L > 0):
  require (Ac + Tem) <= L

Ac - current amount to exchange
La - a period limit for any active member
Lm - a personal limit for a particular member
Tem - total opened and closed orders (total exchanged) for a member in a given period
```

No `Limit #2` is applied in case when all the following limits are set to 0:
- a period limit for any active member
- a personal period limit for a particular member

### Limit #3. Period total limit.

Period total limit should satisfy the following requirement:

```
if (Lt > 0):
  require (Ac + Te) <= Lt

Ac - current amount to exchange
Te - total for all open orders of all active members in a given period
Lt - a total period limit for orders for all active members
```

No `Limit #3` is applied in case when a total period limit is set to 0.

## Future features (not to be implemented in the current version)

* initialization with pre-defined data for members like `totalExchanged`, `totalVoided`, etc.

## Interface

### Exchange Member Interface

##### setDefaultExchangeRate(uint256 _defaultExchangeRate)

Sets a default exchange rate

##### setCustomExchangeRate(bytes32 _memberId, uint256 _customExchangeRate)

Sets a particular member exchange rate by its id

##### setTotalPeriodLimit(uint256 _totalPeriodLimit)

Sets a total period exchange limit for all users

##### setDefaultMemberPeriodLimit(uint256 _defaultMemberPeriodLimit)

Sets a member exchange limit for a period

##### setCustomPeriodLimit(bytes32 _memberId, uint256 _customPeriodLimit)

Sets a particular member exchange limit for a period

### Fee Manager Interface

##### setGsnFee(uint256 _gsnFee)

Sets a default GSN fee

### Fee Claimer Interface

##### withdrawYALLs()

Withdraws almost all YALL tokens

### Member Interface

##### createOrder(uint256 _yallAmount)

A user creates a new order with a specified amount to exchange

### Operator Interface

##### closeOrder(uint256 _orderId, string calldata _paymentDetails)

An operator successfully closes an exchange order

##### cancelOrder(uint256 _orderId, string calldata _cancelReason)

An operator cancels an order failed due some reason

### Super Operator Interface

##### voidOrder(uint256 _orderId)

A super operator voids an already closed order and refunds deposited YALLs

