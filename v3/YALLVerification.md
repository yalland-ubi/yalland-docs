# YALLVerification

The contract provides verifiers with an interface for user and verifier management. Resembles Gnosis MultiSig
`M of N` voting pattern. Any active verifier could propose data for execution. If the proposal reaches `M of N` supports, it automatically executes. In case of failure, it could be executed later by an explicit `excecuteProposal()` method call.

Verifiers manage add/enable/disable actions on other verifiers. Each of these actions should be provided with a new `M` value.

Protocol governance could anytime redeploy another instance of the contract and plug it into the system instead of the current one. This could happen if the current set of verifiers has become dishonest or there is another issue with the contract itself.

## Permissions

### Outbound

The contract has `VERIFICATOR` ACL role which grants it with member management permission in `YALLDistributor` contract.

## Interface

### Verifier Interface

* createProposal() - an active verifier creates proposal by submitting arbitrarily encoded data
* confirmProposal() - an active verifier agrees on a proposed topic; executes a proposal in case if the required threshold has reached
* executeProposal() - executes a non-executed yet proposal

### Self-Interface

Self-Interface implies that the methods could be executed only by submitting and executing a proposal.

* addVerifiers(verifiers[], newM) - adds multiple verifiers
* disableVerifiers(verifiers[], newM) - disables multiple verifiers
* enable verifiers(verifiers[], newM) - enables multiple verifiers
