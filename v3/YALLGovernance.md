# YALLGovernance

A protocol governance contract that has owner permissions for most of the contracts.
It allows YST create and vote for proposals like changing system parameters or upgrading system contracts.

## Implementation details

Contracts will reuse [Aragon Voting](https://github.com/aragon/aragon-apps/blob/7ff724339d2adf41c946b036233d8b8244d8c0bc/apps/voting/contracts/Voting.sol) contract with the following changes:

- upgrade to `Solidity v0.5.x`;
- `YALLStakingForeignMediator`/`YALLStakingHomeMediator` contracts as a source of cached balances;
- different error messages;
- different permission checks;
- can change `voteTime` value for further votes;

The rest features should comply with the aforementioned Voting contract from Aragon including a vote execution decision logic.

## Permissions

The contract doesn't implement `Ownable` trait.

## Interfaces

* [The contract itself](#the-contract-itself)
  * [#changeSupportRequiredPct(uint64 _supportRequiredPct)](#changesupportrequiredpctuint64-_supportrequiredpct)
  * [#changeMinAcceptQuorumPct(uint64 _minAcceptQuorumPct)](#changeminacceptquorumpctuint64-_minacceptquorumpct)
  * [#changeVoteTime(uint64 _voteTimeSeconds)](#changevotetimeuint64-_votetimeseconds)

* [YST Holder Interface](#yst-holder)
  * [#newVote(bytes _executionScript, string _metadata)](#newvotebytes-_executionscript-string-_metadata)
  * [#newVote(bytes _executionScript, string _metadata, bool _castVote, bool _executesIfDecided)](#newvotebytes-_executionscript-string-_metadata-bool-_castvote-bool-_executesifdecided)
  * [#vote(uint256 _voteId, bool _supports, bool _executesIfDecided) external voteExists(_voteId)](#voteuint256-_voteid-bool-_supports-bool-_executesifdecided-external-voteexists_voteid)

* [Permissionless Interface](#permissionless-interface)
  * [#executeVote(uint256 _voteId)](#executevoteuint256-_voteid)

### The contract itself

##### #changeSupportRequiredPct(uint64 _supportRequiredPct)

Set a new supportRequiredPct value. It can be changed only by executing a corresponding proposal.

##### #changeMinAcceptQuorumPct(uint64 _minAcceptQuorumPct)

Set a new minAcceptQuorumPct value. It can be changed only by executing a corresponding proposal.

##### #changeVoteTime(uint64 _voteTimeSeconds)

Set a new vote time value. It can be changed only by executing a corresponding proposal.

### YST Holder
##### #newVote(bytes _executionScript, string _metadata)

Any YST holder even with 1 YST wei can create a new vote proposal.

##### #newVote(bytes _executionScript, string _metadata, bool _castVote, bool _executesIfDecided)

Any YST holder even with 1 YST wei can create a new vote proposal. The additional params like castVote and executesIfDecided are provided.

##### #vote(uint256 _voteId, bool _supports, bool _executesIfDecided) external voteExists(_voteId)

A YST holder casts his vote.

### Permissionless Interface

##### #executeVote(uint256 _voteId)

If the required threshold was reached but the proposal hasn't executed yet due some reason, anyone can trigger its execution.
