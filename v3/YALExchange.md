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
- `#setMemberExchangeRate()`
- `#setDefaultPeriodLimit()`
- `#setMemberPeriodLimit()`
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

## Exchange limits


### Limit #1. Personal exchanged volume limit

The limit is checked once on `createOrder` method call.

A member limit in YAL tokens are calculated by the following formula:

```
Am = Ct - Et - Ot + Vt

Am - max. amount available to exchange
Ct - total claimed amount
Et - total exchanged amount
Ot - total amount for the orders on status OPEN
Vt - total voided amount
```

### Limit #2. Per member/period.

The limit is checked once on `createOrder` method call.

Total exchanged amount by the member in the current period (Ept) is incremented on `#createOrder()` call and is optionally decremented on `#cancelOrder()` call.

Per member/period limit should satisfy the following requirement:

```
L = Lm || La

if (L > 0):
  require (Ac + Tam) <= L
  
if (Lt > 0):
  require (Ac + Ta) <= Lt

Ac - current amount to exchange

Ept - total exchanged amount by the member in the current period
Ta - accummulated period total for all open and closed orders
Tam - accumulated period total for a particular member
Lt - a total period limit for orders for all active members
La - a period limit for any active member
Lm - a personal limit for a particular member

```

No limit #2 is applied in case when the following limits are set to 0:
- a total period limit
- a period limit for any active member
- a personal period limit for a perticular member

## Future features (not to be implemented in the current version)

* initialization with pre-defined data for members like `totalExchanged`, `totalVoided`, etc.
