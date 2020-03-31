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
* #approve() - required by 
* #transfer() - 



## Additional methods

* #transferWithMemo(address _to, uint256 _amount, string calldata _memo) - the same as #transfer() but with
additional string memo which is emitted using corresponding event.
