# YALLDistributor

## Important Updates
### 14/03/2020
- Use lastActivateTimestamp and lastDeActivateTimestamp in structMember and activateMember(), deactivateMember(), claimFunds() functions instead of mapping (uint256 //period// => bool //deactivationStatus//)

### 13/03/2020
-  add mapping (uint256 //period// => bool //deactivationStatus//) public deactivationPerPeriod;
- in deactivateMember() add deactivationPerPeriod.[getCurrentPeriod()] = true;
- check in claimFunds() activationPerPeriod[getCurrentPeriod()-1] == false; 

## Constructor arguments (the values can't be changed later):
- erc20TokenAddress
- periodLength
- startTime - timestamp of the beginning of the first period
- periodVolume - the number of tokens to mint during a single period

## Contract Permissions
### Outbound

The contract has `minter` permissions at YAL(ERC20) contract

### Inbound

* An owner can manage the verifier address and his reward, a reward should comply "<100%" requirement
* A verifier can manage member: add/addMultiple/change/activate/deactivate, claims his rewards 
* Any member which is inside the member list and is active at the moment can claim distributed tokens if he was already included at the beginning of the current period

## Reward calculations

* When a new user joins as a member, he should wait when the next period starts in order to start receiving funds
* An already existing member could claim his funds for a period if he had not claimed it yet
* If a member had deactivated status at beginning of the current period and was activated, he could claim his funds the same way as an already existing user does
* If a member had been deactivated in a current period and had not claimed funds yet, he can't claim the funds unless he will be activated in this period again
* If a member claims his reward in the current period, a verifier performs activate/deactivate actions on the member in the current period, the member can't claim the period reward again since he already did.
* There is no way to delete member completely, only to deactivate him
* Member can't claim funds for a period other than the current
* A member mints his reward on each claim, the reward amount is calculated once within `handlePeriodTransitionIfRequired` modifier
* The funds not claimed by users are not minted, so there is no need to redistribute them

## Dealing with period transition

In order to detect a new period and perform corresponding actions, there is `handlePeriodTransitionIfRequired` modifier introduced. If there is a new period started, the modifier will:
* calculate and cache `rewardPerUser` for the current period.
* calculate and cache `verifierReward` for the current period.

The `activeMemberCount` variable at this time should have exactly the same value as it had at the particular time when the period had started.

The modifier is included into the following methods:

* addMember()
* addMembers()
* activateMember()
* deactivateMember()
* claimFunds()

So each call for the aforementioned methods would cause these checks.

The `handlePeriodTransitionIfRequired` will be replaced with a function if it causes `stack too deep` errors.

## Can claim a reward constraints

To calculate whether a member can claim a reward in this period or not, the following constraints are imposed:

```
// this also implies that Le <= Ld
assert(memberIsActiveNow == true)

if (Ld != 0 && CPid != 0) {
  assert(
      // a member has been both disabled and enabled in the current period
      (Ld >= CPb && Le >= CPb)
      // a member was both disabled and enabled in the previous period
      || (Ld >= PPb && Le >= PPb && Ld < CPb && Le < CPb)
      // a member was both disabled and enabled before the current period started
      || (Ld < CPb && Le < CPb)
  )
}

// Can claim a reward
Ok()

----
CPid - current period ID
CPb - Current period begins at timestamp
PPb - Previous period begins at timestamp
Le - Member last enabled at
Ld - Member last disabled at
```

The code above checks for the major requirement for a reward claiming, ensuring that a member:

* is active now
* was active at the timestamp when the current period started

## Deployment

### YALDistribution
* Owner address will be assigned to  `0x???`
* Initial verifier address will be assigned to `0x???`

### YALToken(ERC20)
* DCity `minter` permissions will be revoked
* DCity `burner` permissions will be revoked
* YALDistribution will be granted with a `minter` role

## Other
* Contract implements Pausable and Ownable traits from OZ
* Contract uses SafeMath from OZ for uint256 values
* Contract allows the owner claiming any amount ETHs, ERC721 tokens and all ERC20 tokens other than the one specified as `token` storage variable
* The contract doesn't need a `burner` role as DCity contract does.

## Prototype code
(won't compile)

```solidity
/*
 * Copyright ©️ 2020 Galt•Project Society Construction and Terraforming Company
 * (Founded by [Nikolai Popeka](https://github.com/npopeka)
 *
 * Copyright ©️ 2020 Galt•Core Blockchain Company
 * (Founded by [Nikolai Popeka](https://github.com/npopeka) by
 * [Basic Agreement](ipfs/QmaCiXUmSrP16Gz8Jdzq6AJESY1EAANmmwha15uR3c1bsS)).
 */

pragma solidity ^0.5.13;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";


// TODO: make pausable
// TODO: make ownable
contract YALDistribution {
  uint256 public genesisTimestamp;
  uint256 public periodLength;
  uint256 public periodVolume;

  uint256 public idCounter;
  IERC20 public token;
  uint256 public activeMemberCount;

  mapping(bytes32 => Member) public members;
  mapping(address => bytes32) public memberAddress2Id;
  // periodId => rewardPerMember
  mapping(uint256 => uint256) public periodRewardPerMember;
  // periodId => amount
  mapping(uint256 => uint256) public verifierPeriodReward;

  address public verifier;
  // 100% == 100 ether
  uint256 public verifierRewardShare;

  struct Member {
    bool active;
    uint256 createdAt;
    address addr;
    // periodId => claimed
    mapping(uint256 => bool) claimedPeriods;
  }

  constructor(
    IERC20 _token,
    uint256 _periodLength,
    uint256 _genesisTimestamp,
    uint256 _periodVolume
  )
    public
  {
    // ..assign
  }

  // Mints tokens, assigns the verifier reward and caches reward per member
  modifier handlePeriodTransitionIfRequired() {
    uint256 currentPeriodId = getCurrentPeriodId();

    if (periodRewardPerMember[currentPeriodId] == 0) {
      verifierPeriodReward[currentPeriodId] = periodVolume * verifierRewardShare;

      // imbalance will be left at the contract
      uint256 currentPeriodRewardPerMember = (periodVolume * (100 ether - verifierRewardShare)) / activeMemberCount;
      assert(currentPeriodRewardPerMember > 0);
      periodRewardPerMember[currentPeriodId] = currentPeriodRewardPerMember;
    }

    _;
  }

  // OWNER INTERFACE

  function setVerifier(address _verifier) external onlyOwner {
    verifier = _verifier;
  }

  function setVerifierRewardShare(uint256 _share) external onlyOwner {
    require(_share < 100 ether);
    verifierRewardShare = _share;
  }

  function setPeriodVolume(uint256 _newVolume) external onlyOwner {
    periodVolume = _newVolume;
  }

  // VERIFIER INTERFACE

  function addMembers(bytes32[] calldata _memberIds, address calldata _addrs) external mintNewPeriodIfRequired onlyVerifier {
    uint256[] memory ids = new uint256(_members.length);

    for (uint256 i = 0; i < _members.length; i++) {
      ids[i] = _addMember(_members[i]);
    }

    return ids;
  }

  function addMember(bytes32 _memberId, bytes32 _member) external mintNewPeriodIfRequired onlyVerifier {
    return _addMember(_member);
  }

  function _addMember(bytes32 _memberId, address _member) internal {
    require(memberAddress2Id[_member] == address(0), "The address already registered");

    Member storage member = members[_memberId];

    member.addr = _member;
    member.active = true;
    member.createdAt = now;

    activeMemberCount++;
  }

  function activateMember(bytes32 _memberId) external handlePeriodTransitionIfRequired onlyVerifier {
    Member storage member = members[id];
    require(member.active == false);
    require(member.createdAt != 0);
    activeMemberCount++;
  }

  function deactivateMember(bytes32 _memberId) external handlePeriodTransitionIfRequired onlyVerifier {
    Member storage member = members[id];
    require(member.active == true);
    activeMemberCount--;
  }

  function changeMemberAddress(bytes32 _memberId, address _to) external onlyVerifier {
    Member storage member = members[id];
    member.addr = _to;
  }

  function claimVerifierReward(uint256 _periodId, address _to) external onlyVerifier {
    // TODO: add already claimed check
    token.mint(_to, verifierPeriodRewards[_periodId]);
  }

  // MEMBER INTERFACE
  // @dev Claims msg.sender funds for the previous period
  function claimFunds(
    uint256 _memberId
  )
    external
    handlePeriodTransitionIfRequired
  {
    Member storage member = members[_memberId];

    require(member.addr == msg.sender, "Address doesn't match");
    require(member.active == true, "Not active member");

    require(member.createdAt < getCurrentPeriodBeginsAt());
    require(member.claimedPeriods[getCurrentPeriodId()] == false);

    member.claimedPeriods[getCurrentPeriodId()] = true;

    token.mint(msg.sender, verifierPeriodReward[getCurrentPeriodId()]);
  }

  function changeMyAddress(bytes32 _memberId, address _to) external {
    Member storage member = members[id];
    require(member.addr = msg.sender);
    member.addr = _to;
  }

  // PERMISSIONLESS INTERFACE
  function handlePeriodTransitionIfRequired() external handlePeriodTransitionIfRequired {
  }

  // INTERNAL METHODS

  function nextId() internal returns (uint256) {
    return ++idCounter;
  }

  // VIEW METHODS

  function getCurrentPeriodId() public view returns (uint256) {
    uint256 blockTimestamp = block.timestamp;

    require(blockTimestamp > genesisTimestamp, "Contract not initiated yet");

    // return (blockTimestamp - genesisTimestamp) / periodLength;
    return (blockTimestamp.sub(genesisTimestamp)) / periodLength;
  }

  function getNextPeriodBeginsAt() public view returns (uint256) {
    if (block.timestamp <= genesisTimestamp) {
      return genesisTimestamp;
    }

    // return ((getCurrentPeriod() + 1) * periodLength) + genesisTimestamp;
    return ((getCurrentPeriodId() + 1).mul(periodLength)).add(genesisTimestamp);
  }

  function getCurrentPeriodBeginsAt() public view returns (uint256) {
    if (block.timestamp <= genesisTimestamp) {
      return genesisTimestamp;
    }

    // return (getCurrentPeriod() * periodLength) + genesisTimestamp;
    return (getCurrentPeriodId().mul(periodLength)).add(genesisTimestamp);
  }
}

    IERC20 _token,
    uint256 _periodLength,
    uint256 _genesisTimestamp,
    uint256 _periodVolume
  )
    public
  {
    // ..assign
  }

  // Mints tokens, assigns the verifier reward and caches reward per member
  modifier handlePeriodTransitionIfRequired() {
    uint256 currentPeriodId = getCurrentPeriodId();

    if (periodRewardPerMember[currentPeriodId] == 0) {
      verifierPeriodReward[currentPeriodId] = periodVolume * verifierRewardShare;

      // imbalance will be left at the contract
      uint256 currentPeriodRewardPerMember = (periodVolume * (100 ether - verifierRewardShare)) / activeMemberCount;
      assert(currentPeriodRewardPerMember > 0);
      periodRewardPerMember[currentPeriodId] = currentPeriodRewardPerMember;
    }

    _;
  }

  // OWNER INTERFACE

  function setVerifier(address _verifier) external onlyOwner {
    verifier = _verifier;
  }

  function setVerifierRewardShare(uint256 _share) external onlyOwner {
    require(_share < 100 ether);
    verifierRewardShare = _share;
  }

  function setPeriodVolume(uint256 _newVolume) external onlyOwner {
    periodVolume = _newVolume;
  }

  // VERIFIER INTERFACE

  function addMembers(address[] calldata _members) external mintNewPeriodIfRequired onlyVerifier returns (uint256[] memory){
    uint256[] memory ids = new uint256(_members.length);

    for (uint256 i = 0; i < _members.length; i++) {
      ids[i] = _addMember(_members[i]);
    }

    return ids;
  }

  function addMember(address _member) external mintNewPeriodIfRequired onlyVerifier returns (uint256) {
    return _addMember(_member);
  }

  function _addMember(address _member) internal returns (uint256) {
    require(memberAddress2Id[_member] == address(0), "The address already registered");

    uint256 id = nextId();

    Member storage member = members[id];

    member.addr = _member;
    member.active = true;
    member.createdAt = now;

    activeMemberCount++;

    return id;
  }

  function activateMember(uint256 _memberId) external handlePeriodTransitionIfRequired onlyVerifier {
    Member storage member = members[id];
    require(member.active == false);
    require(member.createdAt != 0);
    activeMemberCount++;
  }

  function deactivateMember(uint256 _memberId) external handlePeriodTransitionIfRequired onlyVerifier {
    Member storage member = members[id];
    require(member.active == true);
    activeMemberCount--;
  }

  function changeMemberAddress(uint256 _memberId, address _to) external onlyVerifier {
    Member storage member = members[id];
    member.addr = _to;
  }

  function claimVerifierReward(uint256 _periodId, address _to) external onlyVerifier {
    // TODO: add already claimed check
    token.mint(_to, verifierPeriodRewards[_periodId]);
  }

  // MEMBER INTERFACE
  // @dev Claims msg.sender funds for the previous period
  function claimFunds(
    uint256 _memberId
  )
    external
    handlePeriodTransitionIfRequired
  {
    Member storage member = members[_memberId];

    require(member.addr == msg.sender, "Address doesn't match");
    require(member.active == true, "Not active member");

    require(member.createdAt < getCurrentPeriodBeginsAt());
    require(member.claimedPeriods[getCurrentPeriodId()] == false);

    member.claimedPeriods[getCurrentPeriodId()] = true;

    token.mint(msg.sender, verifierPeriodReward[getCurrentPeriodId()]);
  }

  function changeMyAddress(uint256 _memberId, address _to) external {
    Member storage member = members[id];
    require(member.addr = msg.sender);
    member.addr = _to;
  }

  // PERMISSIONLESS INTERFACE
  function handlePeriodTransitionIfRequired() external handlePeriodTransitionIfRequired {
  }

  // INTERNAL METHODS

  function nextId() internal returns (uint256) {
    return ++idCounter;
  }

  // VIEW METHODS

  function getCurrentPeriodId() public view returns (uint256) {
    uint256 blockTimestamp = block.timestamp;

    require(blockTimestamp > genesisTimestamp, "Contract not initiated yet");

    // return (blockTimestamp - genesisTimestamp) / periodLength;
    return (blockTimestamp.sub(genesisTimestamp)) / periodLength;
  }

  function getNextPeriodBeginsAt() public view returns (uint256) {
    if (block.timestamp <= genesisTimestamp) {
      return genesisTimestamp;
    }

    // return ((getCurrentPeriod() + 1) * periodLength) + genesisTimestamp;
    return ((getCurrentPeriodId() + 1).mul(periodLength)).add(genesisTimestamp);
  }

  function getCurrentPeriodBeginsAt() public view returns (uint256) {
    if (block.timestamp <= genesisTimestamp) {
      return genesisTimestamp;
    }

    // return (getCurrentPeriod() * periodLength) + genesisTimestamp;
    return (getCurrentPeriodId().mul(periodLength)).add(genesisTimestamp);
  }
}

```

