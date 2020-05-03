# Deploy Stage #1

The Stage #1 contract are deployed at `xDai` chain only.

## ACL permissions list

|Role|Assigned to|Can call methods|
|---|---|---|
|FEE_MANAGER|external address|YALLExchange->setGsnFee()<br>YALLToken->setGsnFee()<br>YALLToken->setTransferFee()<br>YALLDistributor->setGsnFee()|
|FEE_CLAIMER|external address|YALLDistributor->withdrawFee()<br>YALLExchange->withdrawYALLs()<br>YALLToken->withdrawFee()|
|PAUSER|external address|YALLExchange->pause()<br>YALLExchange->unpause()<br>YALLDistributor->pause()<br>YALLDistributor->unpause()<br>YALLToken->pause()<br>YALLToken->unpause()|
| | | |
|YALL_TOKEN_MINTER|`YALLDistributor`|YALLToken->mint()|
|YALL_TOKEN_BURNER|`YALLDistributor`|YALLToken->burn()|
|YALL_TOKEN_WHITELIST_MANAGER|external address|YALLToken->setWhitelistAddress()|
| | | |
|DISTRIBUTOR_MANAGER|external address|YALLDistributor->setEmissionPoolRewardShare()<br>YALLDistributor->setPeriodVolume()|
|DISTRIBUTOR_VERIFIER|external address|YALLDistributor->addMembersBeforeGenesis()<br>YALLDistributor->addMembers()<br>YALLDistributor->addMember()<br>YALLDistributor->enableMembers()<br>YALLDistributor->disableMembers()<br>YALLDistributor->changeMemberAddresses()<br>YALLDistributor->changeMemberAddress()|
|DISTRIBUTOR_EMISSION_CLAIMER|external address|YALLDistributor->claimEmissionPoolReward()|
| | | |
|EXCHANGE_MANAGER|external address|YALLExchange->setDefaultExchangeRate()<br>YALLExchange->setCustomExchangeRate()<br>YALLExchange->setTotalPeriodLimit()<br>YALLExchange->setDefaultMemberPeriodLimit()<br>YALLExchange->setCustomPeriodLimit()|
|EXCHANGE_OPERATOR|external address|YALLExchange->closeOrder()<br>YALLExchange->cancelOrder()|
|EXCHANGE_SUPER_OPERATOR|external address|YALLExchange->voidOrder()|
