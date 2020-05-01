## YALLToken Ethereum

YALL Token contract for Ethereum mainnet. Will be bridged with xDai YALL Token using Arbitrary Message Bridge (AMB).

## Dependencies

The contract will inherit from:

* `@openzeppelin/contracts/token/ERC20/ERC20.sol`
* `@openzeppelin/contracts/token/ERC20/ERC20Detailed.sol`
* `@openzeppelin/contracts/ownership/Ownable.sol`

## Variables

The contract will be deployed with the following variables:

* Name: "YALL Ethereum"
* Symbol: "YALL"
* Decimals: 18

## GSN support 

There is no support for GSN relayed transactions

## Fees

There is no fees for the Ethereum mainnet version of this token.

## Permissions

## Owner

Owner is a particular address. It has exclusive permissions for:

* `#setMinterAndBurnerAddress()`

## MinterAndBurner

MinterAndBurner is a particular address. It has exclusive permissions for:

* `#mint()`
* `#burn()`

By this design `minterAndBurner` role will be assigned to a ForeignMediator contract
