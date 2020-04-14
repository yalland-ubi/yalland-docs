# YAL Fund Exchange Development Specification

## GSN support

GSN support should be implemented for the following methods:

* #createOrder - a program member creates an exchange order

## Permissions
### Owner

Owner is a particular address. It has exclusive permissions for:

* managing (add/remove actions) all existing roles

### Roles

Multiple addresses can be assigned to a particular role.

#### Fund Manager

Has exclusive permissions to call:

- `#setDefaultExchangeRate()`
- `#setCustomExchangeRate()`
- `#setTotalPeriodLimit()`
- `#setDefaultMemberPeriodLimit()`
- `#setCustomPeriodLimit()`
- `#setGsnFee()`
- `#withdrawYALs()`

#### Operator

- Has exclusive permissions to call:

- `#closeOrder()`
- `#cancelOrder()`

#### Super Operator

- `#voidOrder()`

### YAL active member

Any active YAL program member can call:

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
