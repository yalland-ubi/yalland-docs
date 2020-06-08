# YALLVerification

The contract provides verifiers with an interface for user and verifier management. Resembles Gnosis MultiSig
`M of N` voting pattern. Any active verifier could propose data for execution. If the proposal reaches `M of N` supports, it automatically executes. In case of failure, it could be executed later by an explicit `excecuteProposal()` method call. YALLVerification contract will resemble some part of Gnosis MultiSig for Solidity v0.5.x. It can't inherit it due a different data structures for `owners` representation.

Governance contract/address can perform setVerifiers actions on verifiers. Also, the protocol governance could anytime redeploy another instance of the contract and plug it into the system instead of the current one. This could happen if the current set of verifiers has become dishonest or there is another issue with the contract itself.

Verificator details resemble member details behavior/structure in `YALLDistributor` contract. Data like `createdAt`,
`disabledAt`, `enabledAt` is used to determine whether a validator eligible claiming a particular reward or not.

Verifier details are backed by the following struct:

```
  struct Verifier {
    bool active;

    // used to vote on "add/disable member" proposals; 
    address verificationAddress;
    // used to claim rewards from pools;
    address payoutAddress;
    // used to set data (name, legal, etc)
    address dataManagementAddress
    
    uint256 createdAt;
    uint256 lastEnabledAt;
    uint256 lastDisabledAt;
  }
```

## Permissions

### Outbound

The contract has `DISTRIBUTOR_VERIFIER_ROLE` ACL role which grants it with member management permission in `YALLDistributor` contract.

### Inbound

Governance contract has `VERIFICATION_MANAGER` ACL role and has permission setting a new verifier set in `YALLVerification` contract.

## Supplementary YALLVerifierRegistry contract

YALLVerifierRegistry contract allows any active verifier set his details anytime:

* Name (string)
* IPLD details hash (string)

## Interface

### Verifier Interface

##### Scoped to a validators `verificationAddress`

* submitTransaction() - an active verifier creates proposal by submitting arbitrarily encoded data
* confirmTransaction() - an active verifier agrees on a proposed topic; executes a proposal in case if the required threshold has reached
* revokeConfirmation() - an active verifiers revokes his vote
* executeTransaction() - executes a non-executed yet proposal

##### Scoped to a validators `rootAddress`

* setVerifierAddresses() - an active verifier sets correspoinding `verificationAddress`, `payoutAddress`, `dataManagementAddress`

### VERIFICATION_MANAGER Interface

* setVerifiers(newVerifiers[], newM) - Sets multiple verifiers. The methods walks through the existing verifier set and disables them, if they are not presented in existing. This behaviour requires a nested for-loop, but it is the simpliest way to implement it.
* changeRequirement(newM) - Set a new M value.
