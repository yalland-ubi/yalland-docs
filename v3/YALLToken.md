# YALLToken

ERC20 compatible token with the following functionlaity:

* Additional commission, the amount of which is determined by YALLGovernance;
* Gas Station Network support;
* Token transfer can only be performed between verified users (user status is defined in YALLDistributor) and the whitelisted contracts (determined in token contract);
* Token balance can be restored by YALLDistributor contract.

## Inbound ACL roles

|Role name| Allowed methods |
|---|---|
|`YALL_MINTER`| `mint()`|
|`YALL_BURNER`| `burn()`|
|`FEE_MANAGER`| `setGsnFee()`, `setTransferFee()`|
|`FEE_CLAIMER`| `withdrawFee()`|

## GSN support

GSN supports the following methods:
* `#approve()` - approver pays for the transaction in YAL tokens;
* `#transfer()` - msg.sender account pays for the transactin in YAL tokens;
* `#transferWithMemo()` - the same as `#transfer()` but has an additional `memo` string argument
* `#transferFrom()` - msg.sender account pays for the transaction in YAL tokens;

## Fees

There are 2 fees imposed on member methods:

* `transferFee` (in %) is calculated on top of the amount being transferred;
* `gsnFee` is fixed value in YALL.

|Method|GSN Support|GSN Fee|Transfer Fee|
|---|---|---|---
|`approve()`|V|V|X|
|`transfer()`|V|V|V|
|`transferWithMemo()`|V|V|V|
|`transferFrom()`|V|V|V|
|`increaseAllowance()`|X|X|X|
|`decreaseAllowance()`|X|X|X|

Example:
```
For transferFee = 1% and gsnFee = 0.03 ETH

amount to transfer - 1 ETH
transferFee = 0.01 ETH
gsnFee = 0.03 ETH
total = 1.04 ETH
```

For negligible amounts it is possible to have 0 fee even when `transferFee > 0`, for ex:

```
For transferFee = 20%

amount to transfer - 4 wei
transferFee = 0 wei
total = 4 wei
```

## Pausable methods

`YALLToken` contract implements `ACLPausable` trait. If the contract is paused, execution of the following methods will be reverted:
* `mint()`
* `burn()`
* `approve()`
* `transfer()`
* `transferWithMemo()`
* `transferFrom()`
* `increaseAllowance()`
* `decreaseAllowance()`

## Additional methods

* `#transferWithMemo(address _to, uint256 _amount, string calldata _memo)` - the same as `#transfer()` but with
additional string memo which is emitted using corresponding event.

## Other requirements

* `#transfer()`, `#transferFrom()`, `#approve()` method `from`/`to`/`msg.sender` arguments should represent either:
  - the active holder in YALLDistributor contract;
  - or an allowed address in a specific whitelist residing in ERC20 contract;

If one of them is not in the active list, the transfer will revert.

* `#transferFrom()` transaction msg.sender could either reside in YALLDisctributor active user list or in a specific whitelist to be eligible executing this method.
* The aforementioned whitelist for `#transferFrom()` is public and can be managed by `YALL_TOKEN_WHITELIST_MANAGER` ACL role

