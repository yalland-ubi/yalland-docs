# YALLHomeMediator and YALLForeignMediator

Bridges YALLToken with YALLTokenEthereum. YALLHomeMediator is deployed at xDai chain while YALLForeignMediator
is deployed at Ethereum Mainnet

POA AMB contracts were used with a few changes. One important change is that Home mediator locks/unlocks tokens while Foreign
mediator mints/burns tokens.

More about AMB contracts here https://docs.tokenbridge.net/amb-bridge/how-to-develop-xchain-apps-by-amb.

## Restrictions

You can't change these fields after the contract initialization:

* ERC20 contract address

## Limits

There are 5 limit for a mediator outbound transfers:

* DAILY_LIMIT
* MAX_PER_TX
* MIN_PER_TX

And inbound transfers:

* EXECUTION_DAILY_LIMIT
* EXECUTION_MAX_PER_TX

## Upgradeability Owner Inteface

* `function claimTokens(address _token, address _to) public;` - transfer stuck ETH/ERC20 tokens
* `function fixAssetsAboveLimits(bytes32 txHash, bool unlockOnForeign, uint256 valueToUnlock) external;`


## Owner Interface

* `function setBridgeContract(address _bridgeContract) external;`
* `function setMediatorContractOnOtherSide(address _mediatorContract) external;`
* `function setRequestGasLimit(uint256 _requestGasLimit) external;`
* `function setDailyLimit(uint256 _dailyLimit) external;`
* `function setExecutionDailyLimit(uint256 _dailyLimit) external;`
* `function setExecutionMaxPerTx(uint256 _maxPerTx) external;`
* `function setMaxPerTx(uint256 _maxPerTx) external;`
* `function setMinPerTx(uint256 _minPerTx) external;`

## Bridge Interface

* `function handleBridgedTokens( address _recipient, uint256 _value, bytes32 /* nonce */) external;`
* `function fixFailedMessage(bytes32 _dataHash) external;`

## Permissionless Interface

*  `function requestFailedMessageFix(bytes32 _txHash) external;`

