# AMB between Ethereum and xDai chains

* Foreign chain: Ethereum, `YALLEthereum` tokens are` minted/burned` by a ForeignMediator here
* Home chain: xDai, `YALL` tokens are `locked/unlocked` at a HomeMediator here

## Deployment variables

The contract will be deployed with the following variables.

### Ethereum Mainnet

* _bridgeContract: address of POA AMB contract, current address is listed here https://docs.tokenbridge.net/eth-xdai-amb-bridge/about-the-eth-xdai-amb
* _mediatorContract(onOtherSide): corresponding value of Home Mediator
* _erc677token: corresponding address of YALLTokenEthereum contract
* _dailyLimitMaxPerTxMinPerTxArray: `???` [ 0 = _dailyLimit, 1 = _maxPerTx, 2 = _minPerTx ]
* _executionDailyLimitExecutionMaxPerTxArray: `???` [ 0 = _executionDailyLimit, 1 = _executionMaxPerTx ]
* _requestGasLimit: 2_000_000
* _decimalShift: `???`

### xDai chain

* _bridgeContract: address of POA AMB contract, current address is listed here https://docs.tokenbridge.net/eth-xdai-amb-bridge/about-the-eth-xdai-amb
* _mediatorContract(onOtherSide): corresponding address either of Foreign Mediator
* _erc677token: corresponding address of YALLToken contract
* _dailyLimitMaxPerTxMinPerTxArray: `???` [ 0 = _dailyLimit, 1 = _maxPerTx, 2 = _minPerTx ]
* _executionDailyLimitExecutionMaxPerTxArray: `???` [ 0 = _executionDailyLimit, 1 = _executionMaxPerTx ]
* _requestGasLimit: 2_000_000
* _decimalShift: `???`

More details on `_dailyLimitMaxPerTxMinPerTxArray` and `_executionDailyLimitExecutionMaxPerTxArray` here https://github.com/poanetwork/tokenbridge-contracts/blob/dfe427b5fd4d57ebdf7bddc659aa780b752777e7/contracts/upgradeable_contracts/amb_erc677_to_erc677/BasicAMBErc677ToErc677.sol#L43
## Proxy
Both HomeMedaitor and ForeignMediator are wrapped with a proxy

## Permissions:
### Owner

Owner is a particular address. It has exclusive permissions for:

* `#setBridgeContract()`
* `#setMediatorContractOnOtherSide()`
* `#setRequestGasLimit()`

### UpgradeabilityOwner

UpgradeabilityOwner is a particular address. It has exclusive permissions for:

* `#fixAssetsAboveLimits()`
* `#claimTokens()`

## ERC677

Although the reference AMB contracts support ERC677 standard, this AMB implementation won't support it.
YALLToken and YALLTokenEthereum don't support ERC677 as well.
This means that if the funds were transferred to one of the mediators without calling,
it wouldn't transfer them to an opposite chain.
The mediator owner would become a new owner of these funds and would dispose of them as he wishes.

