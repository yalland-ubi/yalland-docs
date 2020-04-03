# Yalland Tokens
## Tokens Overview
- **Yall ERC20 Token(YALL)** - ERC20 standard token with the following additional functions: balance caching per block, the ability to specify a note as an arbitrary line when the token transfer is performed, token transfer can be performed only between verified users and whitelisted smart contracts, compatibility with Gas Station Network(GSN). YALL has a limited supply. YALL is transferable between  different chains. For current implementation it’s initial created on xDAI chain and transferable to Ethereum mainnet and back via ETH-xDai Arbitrary Message Bridge.   
- **Yall ERC20 Staking Token(YST)** - ERC20 standard token with the following additional functions: balance caching per block. The main purpose of the token is the decentralized management of smart contracts of the system of universal basic income. Token has fixed supply. It’s initially created on Ethereum mainnet as a token of Aragon organisation. It can be transfered to xDAI chain and staked.
## Yall ERC20 Token(YALL)
### Emission model
### Transfer between chains
Tokens is transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). The number of transferred tokens is limited to 100 000 YALL 
## Yall ERC20 Staking Token(YST)
### Emission model
### Transfer between chains
Tokens is transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). The number of transferred tokens is not limited either from above or from below.
In the future, it is possible to transfer a token to an ETH 2.0, Aragon chain, Yalland chain (if implemented) or any other reliable chain with high bandwidth and low transaction cost. 
# Yalland SideChain contracts overview

# References
- [Arbitrary Message Bridge](https://docs.tokenbridge.net/amb-bridge/about-amb-bridge);
- [GSN Frequently Asked Questions](https://docs.openzeppelin.com/gsn-provider/0.1/gsn-faq#how_does_it_work)
