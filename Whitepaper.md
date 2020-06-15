# Yalland Whitepaper
## Yalland universal basic income overview
The program is a public blockchain smart contract system managed by a decentralized community. Everyone can participate in the program, verifying himself/herself as a unique person and receive points (tokens), which can be exchanged for products, services or fiat money on the open market.
The program is not state-owned and is built on the principles of P2P - economics.
The presented smart contract system is blockchain agnistic and can be executed in any Turing-complete virtual machine. It is assumed that it will work initially on Ethereum + xDai and then will be migrated to Ethereum 2.0.
## Yalland Basic economic principles
Meet Alice, Bob and Dan, Philanthropist and Speculator! Alice is a high income person, Bob has just lost his job and he does not mind a little extra cash, Dan runs a grocery store. Philanthropist has a lot of assets and is very concerned about social stability. The Speculator just wants to make some profit.
- Alice, Bob and Dan all participate in Yalland UBI program because they share the values of social stability and mutual assistance. The all get 100 YALL each month;
- Bob goes to Dan and buys products for a month for 50 YALL;
- Dan goes to open market and sells 50 YALL for 200 USD;
- Alice also has 100 YALL. Unlike Bob, she consumes more goods, since she has a larger income. Since she supports UBI values and prefers to pay in YALL, she buys 100 YALL for 400 USD on the open market and pays 200 YALL to Dan;
- Philanthropist periodically comes to the market and maintains the YALL rate, by buying them from the market;
- And, of course, there is a Speculator on the market who buys and sells YALL willing to profit from price fluctuations. Sometimes he succeeds, sometimes not.
<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-4.png" alt="Yalland Basic economic principles" width="400"/></p>

Thus, the wealthier and more successful Alice, Philanthropist and Speculator are through natural market mechanisms, the more support Bob receives in difficult times. The whole process takes place without unnecessary intermediaries represented by the state.
## Roles
- **Yalland UBI program member** - a unique person, who has proved that he is a unique person and receives YALL tokens for the purchase of goods and services;
- **Verifier** - A non-profit organizations or foundation that confirm the uniqueness of people in the program and their right to receive UBI in YALL tokens;
- **Delegator** -  YST token holder, who elects Verifiers by voting, upgrades smart contracts and changes their parameters.

## Tokens Overview
- **Yalland ERC20 Token(YALL)** - ERC20 standard token with the following additional functionality: the ability to specify a note as an arbitrary line when the token transfer is performed, token transfer can be performed only between verified users and whitelisted smart contracts, compatibility with Gas Station Network(GSN), 
transfer fee. YALL has a limited supply. YALL is transferable between  different chains. For current implementation it is initially created on xDAI chain and is transferable to Ethereum mainnet and back via ETH-xDai Arbitrary Message Bridge.   
- **Yall ERC20 Staking Token(YST)** - ERC20 standard token. The main purpose of the token is the decentralized management of smart contracts of the system of universal basic income. Token has fixed supply and can be staked for governance purposes.
## Program member verification
In order to receive UBI, each person who wants to participate in the program must periodically confirm that he/she is a unique person. This is a prerequisite for an honest distribution of universal basic income. For these purposes, two mechanisms are used:
- the mechanism of delegated decentralized verification;
- the mechanism of complete decentralized verification.
Initially, only the first mechanism is used. 
In the future, it will be completely replaced by the second one.
### Delegated decentralized verification (DDV)
YST token Holders' main task is to decentralize UBI system governance, add and remove Verifiers by voting. As a reward they receive a part of the YALL tokens emission from `Emission Reward Pool contract`.
Each Verifier is a non-profit organization registered in the relevant jurisdiction in accordance with local laws. They identify UBI program participants and allow them to receive YALL tokens. For identification, they use documents, open sources and government databases. Also, they store personal data of participants strictly in accordance with local laws in a shared database. It is important to note that Verifiers must have a deposit both in YALL and YST tokens. If the Verifier is removed, its security deposit is debited in favor of the YST Holders.
<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-5.png" alt="Delegated decentralized verification" width="700"/></p>
### Complete decentralized verification (CDV)
TBD
### Verified user address change
A situation may arise when the private key of a program participant has been compromised. In this case, assuming a person has the old private key, he/she can set his/her new address without having to re-verify.
To do this, the Program Member sends a transaction signed by the old private key containing the new address to `YALL Distribution Contract`. The balance from the old address is automatically transferred to the new one.
### Verified user address recovery
In case of loss of the private key, the Program Member can restore its balance and change the old address to the new one.
Verifiers perform this operation. After the identity of the program member is confirmed, Verifiers (one or several) confirm the change of the address. The `YALL Distribution Contract` contract burns YALL from old address and mints the same amount to the new one.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-10.png" alt="Verified user address recovery" width="700"/></p>

## Yalland Tokens
### Yall ERC20 Token(YALL)
#### Purpose
YALL has the following purposes:
- providing each person with the minimum amount of goods and services necessary for life;
- a means of payment for goods and services;
- a redistribution of excess profits. 
#### Emission model
Each program participant can receive YALL as a Universal Basic Income. YALL tokens are issued in periods of 7 days. The maximum number of YALL that can be issued per period is 275 000. 10% go to the `Reward Pool contract` to reward Verifiers and Delegators at the beginning of each period. The remaining 247 500 YALL is distributed among program participants. At the same time, each program participant must independently claim YALL by sending transaction to `YALL Distribution smart contract`, which has the right to mint tokens.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-3.png" alt="Emission model" width="700"/></p>

After 264,000,000 YALL is minted, YALL initial emission will cease and users will receive a weekly payment only from the Commission pool contract.
#### YALL transfer between chains
Tokens are transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). Thanks to this, YALL can be traded on centralized and decentralized exchanges. Mediator contracts are managed by `Governance Proposal manager contract`.

`Governance Proposal manager contract` can:
- change YALL token address on both chains;
- change mediator contract on both chains;
- define mediator parameters: Min per transaction, Max per transaction, Transaction limit max per day.

Any owner can send his/her YALL tokens to the Ethereum mainnet. To do this, Owner transfers his/her YALL to `YALL ERC20 MEDIATOR contract`. This contract, in turn, transmits the message through the AMB to the mediator, which is located in the Ethereum mainchain. `YALL ERC20 MEDIATOR` in Ethereum issues the correct amount of tokens. When sent back, Yall tokens are burned in Ethereum and released in xDai.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-1.png" alt="YALL Transfer between chains" width="700"/></p>

### Yall ERC20 Staking Token(YST)
#### YST purpose
YST has the following purposes:
- decentralized governance of the universal basic income system;
- creation of economic incentives for decentralized governance.
#### YST emission model
YST has fixed supply, issued once, may not have additional emissions(minted) or be destroyed(burned).
#### YST staking 
YST token can be staked by their Owners on Ethereum mainnet. The Owner of the token sends it to the `YALL STAKING MEDIATOR contract`. The token is blocked in the contract and the Owner of the token receives a blocked balance, which is simultaneously available in both Ethereum and xDai. 
Using this balance, the token holder can create proposals for upgrading smart contracts and changing their parameters, as well as, elect Verifiers.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-2.png" alt="YST staking" width="700"/></p>

#### YST transfer between chains
Tokens are not transferable between Ethereum mainnet and xDai sidechain via Arbitrary Message Bridge (AMB). In the future, it is possible to be able to transfer tokens to ETH 2.0, Aragon chain, Yalland chain (if implemented) or any other reliable chain with high bandwidth and low transaction cost. 
### Emission Reward pool
A certain percentage of the total YALL issuance (currently 10%) goes to the total emission pool to reward Verifiers and Delegators. Initially, their remuneration is divided 50% / 50%, but this proportion can be changed.
At the beginning of each token issuance period `YALL Distribution Contract` calls the `YALL ERC20 Contract`, which, in return mints, tokens on the `Emission Reward Pool contract`.
Verifiers receive 5% of the emissions distributed in equal parts between them. Delegators receive 5% of the emission, distributed in proportion to their Stake.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-6.png" alt="Emission pool" width="700"/></p>

### Commission Reward Pool
All operations with YALL have a commission, which is established by the `Governance Proposal manager contract`. The commission from each transfer goes to the `Commission Reward Pool Contract`. Until the end of the initial YALL distribution, this commission is divided between the Verifiers (proportionally to total number of Verifiers) and the Delegators (proportionally to their own steak) in proportions determined by `Governance Proposal manager contract`.

After the end of the initial YALL distribution, part of the commission goes to payments to program participants through YALL Distribution Contract.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-7.png" alt="Commission Reward Pool" width="700"/></p>

### Governance
Decentralized governance of the Yalland UBI system is performed by Delegators. They stake YST token to smart contract on Ethereum mainnet and vote by their stake. They can vote on different chains (Ethereum and xDai) simultaneosly. They upgrade smart contract, elect verifiers and set contract parameters. All those operations are performed by `Governance Proposal manager contract`. Delegators create proposals and vote for them. 
`Governance Proposal manager contract` has the following parameters:
- `support` = 51% - the number of votes "Yes" required to make a decision;
- `duration` = 7 days - voting duration;
- `minimum support` = 10%;
Voting takes place according to the following algorithm:
- Delegators casts a vote with his stake;
- if voting is not closed and `support` > 51%, the decision is made;
- if voting is  closed and `support` > `minimum support`, the decision is made.

`Support`, `duration` and `minimum support` can be changed.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-8.png" alt="Governance" width="700"/></p>

### Gas Station Network
To improve the user experience, the Gas Station Network is used. This system allows Member to pay a gas commission not with a network token, but with ERC20. A program participant creates a transaction in the wallet and sends it to the Relayer. The Relayer signs the transaction and sends it to the corresponding smart contracts via the GSN. Relayer pays for gas and receives a commission in YALL from Member.

<p align="center"> <img src="https://raw.githubusercontent.com/yalland-ubi/yalland-docs/master/images/yalland-9.png" alt="Governance" width="700"/></p>

### Development Roadmap
|Period|Scope|Status|
|-----|-------|-------|
|April 2020|New Yalland distribution in Yalland chain|Done|
|April 2020|New Token contract in Testnet|Done|
|April 2020|New Token contract in in Yalland chain|In progress|
|April 2020|GSN network in Testnet|Done|
|April 2020|Sidechain => Mainnet Bridge for YALL transfer in Testnet|Done|
|April 2020|Smart contract migration to xDai|In progress|
|May 2020|Yalland Staking token in Testnet|In progress|
|May 2020|Yalland Staking token in Ethereum mainnet|In progress|
|May 2020|Yalland Staking token Bridge in Testnet|In progress|
|May 2020|Yalland Staking Proposal manager in Testnet|In progress|
|May 2020|Emission pool in Testnet|In progress|
|May 2020|Commission pool in Testnet|In progress|
|May 2020|Yalland Staking token Bridge in Ethereum mainnet + xdai; Yalland Staking Proposal manager in xDai;Emission pool; Commission pool|In progress|

## References
- [Arbitrary Message Bridge](https://docs.tokenbridge.net/amb-bridge/about-amb-bridge);
- [GSN Frequently Asked Questions](https://docs.openzeppelin.com/gsn-provider/0.1/gsn-faq#how_does_it_work)
