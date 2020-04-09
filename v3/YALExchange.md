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

- #setDefaultExchangeRate()
- #setMemberExchangeRate()

#### Operator

- Has exclusive permissions to call:

- #closeOrder()
- #cancelOrder()

#### Super Operator

- #voidOrder()

### YAL active member

Any active YAL program member can call:

- #createOrder()

## Exchange limits per member

A member limits in YAL tokens are calculated by the following formula:

```
Am = Ct - Et - Ot + Vt

Am - max. amount available to exchange
Ct - total claimed amount
Et - total exchanged amount
Ot - total amount for the orders on status OPEN
Vt - total voided amount
```
