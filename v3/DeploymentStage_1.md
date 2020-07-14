# Deploy Stage #1

The Stage #1 contract are deployed at `xDai` and `Ethereum Mainnet` chains.

![Stage #1 Deployment](../images/deployment-stage-1.svg)

## External address list

|Code|Address|
|---|---|
|`extAdminMultiSig`| 0x10cB408e1285d840CACed4d730dF2359e3d56444 |
|`extFeeCollector`| 0x10cB408e1285d840CACed4d730dF2359e3d56444 |
|`extGsnFeeCollector`| 0x10cB408e1285d840CACed4d730dF2359e3d56444 |

## Initial values
The values not supposed to be imported from the previous contracts.

#### Common

* GSN Fee for all contracts: 100_000 YALL (to prevent from using it right now)

#### YALLToken

* Name: "YALL Token"
* Symbol: "YALL"
* Decimals: 18
* Transfer Fee: 0%
* TransferRestrictionsMode: `OFF`, other options: [`OFF`, `ONLY_MEMBERS`, `ONLY_WHITELIST`, `ONLY_MEMBERS_OR_WHITELIST`]
* CanTransferWhitelist Addresses:
  * `yallDistributor`
  * `yallExchange`
  * `yallHomeMediator`
  * `extAdminMultiSig`
  * `extFeeCollector`
  * `extGsnFeeCollector`
* NoTransferFeeWhitelist Addresses:
  * `yallDistributor`
  * `yallExchange`
  * `yallHomeMediator`
  * `extFeeCollector`
  * `extGsnFeeCollector`

#### YALLTokenEthereum

* Name: "YALL Token Ethereum"
* Symbol: "YALE"
* Decimals: 18
* Initial supply: 0

#### YALLTokenHomeMediator

* DAILY_LIMIT (outbound): 500_000
* MAX_AMOUNT_PER_TX (outbound): 500_000
* MIN_AMOUNT_PER_TX (outbound): 500_000
* EXECUTION_DAILY_LIMIT (inbound): `DAILY_LIMIT` value from a Foreign Mediator
* MAX_AMOUNT_PER_TX (inbound): `MAX_AMOUNT_PER_TX` value from a Foreign Mediator
* MEDIATOR_REQUEST_GAS_LIMIT: 2_000_000

#### YALLTokenForeignMediator

* DAILY_LIMIT (outbound): 500_000
* MAX_AMOUNT_PER_TX (outbound): 500_000
* MIN_AMOUNT_PER_TX (outbound): 500_000
* EXECUTION_DAILY_LIMIT (inbound): `DAILY_LIMIT` value from a Home Mediator
* MAX_AMOUNT_PER_TX (inbound): `MAX_AMOUNT_PER_TX` value from a Home Mediator
* MEDIATOR_REQUEST_GAS_LIMIT: 2_000_000

## Contract list

|Chain|Instance Code|Contract|Owner|Proxy Owner|Arbitrary ERC20 withdrawal|ETH Withdrawal|Pausable|
|---|---|---|---|---|---|---|---|
|xDai|yallRegistry|YALLRegistry|extAdminMultiSig|extAdminMultiSig|no|no|no|
|xDai|yallProxyAdmin|ProxyAdmin|extAdminMultiSig|no proxy|no|no|no|
|xDai|yallToken|YALLToken|not ownable|yallProxyAdmin|only YALL by FEE_CLAIMER role|no|particular methods|
|xDai|yallDistribution|YALLDistributor|not ownable|yallProxyAdmin|only YALL by FEE_CLAIMER role|no|particular methods|
|xDai|yallExchange|YALLExhange|not ownable|yallProxyAdmin|only YALL by FEE_CLAIMER role|no|particular methods|
|xDai|yallHomeMediator|YALLTokenHomeMediator|extAdminMultiSig|extAdminMultiSig|any ERC20 by a Proxy Owner|by a Proxy Owner|no|
|Ethereum Mainnet|yallForeignMediator|YALLTokenForeignMediator|extAdminMultiSig|extAdminMultiSig|any ERC20 by a Proxy Owner|by a Proxy Owner|no|
|Ethereum Mainnet|yallTokenEthereum|YALLTokenEthereum|extAdminMultiSig|no proxy|no|no|no|

## ACL permissions list

|Role|Assigned to|Can call methods|
|---|---|---|
|FEE_MANAGER|...|YALLExchange->setGsnFee()<br>YALLToken->setGsnFee()<br>YALLToken->setTransferFee()<br>YALLDistributor->setGsnFee()|
|FEE_CLAIMER|no one|reserved for future use|
|PAUSER|...|YALLExchange->pause()<br>YALLExchange->unpause()<br>YALLDistributor->pause()<br>YALLDistributor->unpause()<br>YALLToken->pause()<br>YALLToken->unpause()|
| | | |
|YALL_TOKEN_MINTER|`yallDistributor`|YALLToken->mint()|
|YALL_TOKEN_BURNER|`yallDistributor`|YALLToken->burn()|
|YALL_TOKEN_MANAGER|...|YALLToken->setCanTransferWhitelistAddress()<br>YALLToken->setNoTransferFeeWhitelistAddress()<br>YALLToken->setTransferRestrictionMode()|
| | | |
|DISTRIBUTOR_MANAGER|...|YALLDistributor->setEmissionPoolRewardShare()<br>YALLDistributor->setPeriodVolume()|
|DISTRIBUTOR_VERIFIER|...|YALLDistributor->addMembersBeforeGenesis()<br>YALLDistributor->addMembers()<br>YALLDistributor->addMember()<br>YALLDistributor->enableMembers()<br>YALLDistributor->disableMembers()<br>YALLDistributor->changeMemberAddresses()<br>YALLDistributor->changeMemberAddress()|
|DISTRIBUTOR_EMISSION_CLAIMER|...|YALLDistributor->distributeEmissionPoolReward()|
| | | |
|EXCHANGE_MANAGER|...|YALLExchange->setDefaultExchangeRate()<br>YALLExchange->setCustomExchangeRate()<br>YALLExchange->setTotalPeriodLimit()<br>YALLExchange->setDefaultMemberPeriodLimit()<br>YALLExchange->setCustomPeriodLimit()|
|EXCHANGE_OPERATOR|...|YALLExchange->closeOrder()<br>YALLExchange->cancelOrder()|
|EXCHANGE_SUPER_OPERATOR|...|YALLExchange->voidOrder()|

## Contract-scoped Roles
|Instance Code|Role|AssignedTo|
|---|---|---|
|`yallTokenEthereum`|`minter`|`yallTokenForeignMediator`|
