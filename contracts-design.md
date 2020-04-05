# Yalland Project Smart Contracts Design

## Roles
- **Yalland UBI program member** - a unique person who has proved that he is a unique person and receives YALL tokens for the purchase of goods and services;
- **Verifiers** - association of non-profit organizations and foundations that confirm the uniqueness of people in the program and their right to receive UBI in YALL tokens;
- **Delegators** -  YST token holders, which elect Verifiers by voting, upgrade smart contracts and change their parameters.

## Yalland Tokens
### Tokens Overview
- **Yalland ERC20 Token(YALL)** - ERC20 standard token with the following additional functions: balance caching per block, the ability to specify a note as an arbitrary line when the token transfer is performed, token transfer can be performed only between verified users and whitelisted smart contracts, compatibility with Gas Station Network(GSN), 
transfer fee. YALL has a limited supply. YALL is transferable between  different chains. For current implementation it’s initial created on xDAI chain and transferable to Ethereum mainnet and back via ETH-xDai Arbitrary Message Bridge.   
- **Yall ERC20 Staking Token(YST)** - ERC20 standard token with the following additional functions: balance caching per block. The main purpose of the token is the decentralized management of smart contracts of the system of universal basic income. Token has fixed supply. It’s initially created on Ethereum mainnet as a token of Aragon organisation. It can be staked.
### Yall ERC20 Token(YALL)
#### Purpose
YALL has the following purposes:
- providing each person with the minimum amount of goods and services necessary for life;
- means of payment for goods and services;
- redistribution of excess profits. 
#### Program member verification
In order to receive UBI, each person who wants to participate in the program must periodically confirm that he (or she) is a unique person. This is a prerequisite for an honest distribution of basic income. For these purposes, two mechanisms are used:
- the mechanism of delegated decentralized verification;
- the mechanism of complete decentralized verification.
Initially, only the first mechanism is used. In the future, it will be completely replaced by a second one.
##### Delegated decentralized verification
##### Complete decentralized verification
#### Emission model
Each program participant can receive YALL as a Universal Basic Income for paying for goods and services. YALL tokens are issued in periods of 7 days. The maximum number of YALLs that can be issued per period is 275 000. 10% go to the `Reward Pool contract` to reward Verifiers and Delegators at the beginning of each period. The remaining 245 000 YALLs are distributed among program participants. At the same time, each program participant must independently receive YALL by sending transaction to `YALLDistribution smart contract`, which has the right to mint tokens.
![Emission model](https://github.com/yalland-ubi/yalland-docs/blob/npopeka-patch-2/images/yalland-3.png)
After 264,000,000 YALL has been minted, YALL initial emission will cease and users will receive a weekly payment only from the Commission pool contract.
#### YALL transfer between chains
Tokens is transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). Thanks to this, YALL can be traded on centralized and decentralized exchanges. Mediator contracts are managed by `Governance Proposal manager contract`.

`Governance Proposal manager contract` can:
- change YALL token address on both chains;
- change mediator contract on both chains;
- define mediator parameters: Min per transaction, Max per transaction, Transaction limit max per day.

Any owner can send his YALL tokens to Ethereum mainnet. To do this, Owner transfers his Yall to `YALL ERC20 MEDIATOR` contract. This contract, in turn, transmits the message through the AMB to the mediator, which is located in Ethereum mainchain. `YALL ERC20 MEDIATOR` in Ethereum issues the right amount of tokens. When sent back, Yall tokens are burned at Ethereum and released on xDai.
![YALL Transfer between chains](https://github.com/yalland-ubi/yalland-docs/blob/npopeka-patch-2/images/yalland-1.png)
### Yall ERC20 Staking Token(YST)
#### YST purpose
YST has the following purposes:
- decentralized governance of the universal basic income system;
- creation of economic motivation for decentralized governance.
#### YST emission model
YST has fixed supply, issued once, may not have additional emissions(minted) or be destroyed(burned).
#### YST staking 
YST token can be staked by their Owners on Ethereum mainnet. The Owner of the token sends it to the `YALL STAKING mediator contract`. The token is blocked in the contract and the Owner of the token receives a blocked balance, which is simultaneously available in both Ethereum and xDai. 
Using this balance, the token holder can create proposals for upgrading smart contracts, changing their parameters and elect Verifiers.
![YALL Transfer between chains](https://github.com/yalland-ubi/yalland-docs/blob/npopeka-patch-2/images/yalland-2.png)
#### YST transfer between chains
Tokens are not transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). In the future, it is possible to be able to transfer tokens to ETH 2.0, Aragon chain, Yalland chain (if implemented) or any other reliable chain with high bandwidth and low transaction cost. 
## References
- [Arbitrary Message Bridge](https://docs.tokenbridge.net/amb-bridge/about-amb-bridge);
- [GSN Frequently Asked Questions](https://docs.openzeppelin.com/gsn-provider/0.1/gsn-faq#how_does_it_work)
