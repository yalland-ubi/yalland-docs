# CoinToken

ERC20 compatible token with the following functionlaity:

* ERC20 - basic functionality
* ERC20Pausable - trigger for pausing a contract (onlyPauser modifier is overriden)
* ERC20Burnable - 'burner' role can burn tokens
* ERC20Detailed - token info like name, token name, decimals
* Checkpointable - MiniMe-like caching
* Permissionable - roles
* GSNRecipientSigned - GSN support


## GSN support

GSN supprots the following methods:
* `#approve()` - approver pays for the transaction in YAL tokens
* `#transfer()` - from account pays for the transactin in YAL tokens


## Fees
### TransferFee
* `transferFee` now is calculated on top of the amount being transferred
* `gsnFee` is calculated on top of the amount being transferred

Example:
```
For transferFee = 1% and gsnFee = 3%

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

* `#transfer()`, `#transferFrom()`, `#approve()` method from/to arguments should represent only the active holders in YALDistributor contract

