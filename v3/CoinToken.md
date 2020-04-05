# CoinToken

ERC20 compatible token with the following functionlaity:

* ERC20 - basic functionality
* ERC20Pausable - trigger for pausing a contract (onlyPauser modifier is overriden)
* ERC20Burnable - 'burner' role can burn tokens
* ERC20Detailed - token info like name, token name, decimals
* Checkpointable - MiniMe-like caching
* Permissionable - roles
* GSNRecipientSigned - GSN support





## Fees
* `transferFee` now is calculated on top of the amount being transferred;
* `gsnFee` is fixed and is defined by the additinal role;

### GSN fee

GSN supprots the following methods:
* `#approve()` - msg.sender (approver) pays for the transaction in YAL tokens;
* `#transfer()` - msg.sender(from) account pays for the transactin in YAL tokens;
* `#transferWithMemo()` - the same as `#transfer()`
* `#transferFrom()` - msg.sender pays for the transaction in YAL tokens;

### Transfer fee

Transfer fee is charged for the following methods:
* `#transfer()` - msg.sender(from) account pays for the transactin in YAL tokens;
* `#transferWithMemo()` - the same as `#transfer()`
* `#transferFrom()` - msg.sender pays for the transaction in YAL tokens;

### Examples
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

## Additional methods

* `#transferWithMemo(address _to, uint256 _amount, string calldata _memo)` - the same as `#transfer()` but with
additional string memo which is emitted using corresponding event.

## Other requirements

* `#transfer()`, `#transferFrom()`, `#approve()` method from/to/msg.sender arguments should represent either:
- the active holder in YALDistributor contract;
- or an allowed address in a specific whitelist residing in ERC20 contract;

If one of them is not in the active list, the transfer will revert.

* `#transferFrom()` transaction msg.sender could either be in YALDisctributor active user list or in a specific whitelist for eligible performing this method.
* The aforementioned whitelist for `#transferFrom()` is public and can be managed by `transfer_wl_manager` role

