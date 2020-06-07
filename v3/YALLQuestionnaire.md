# YALLQuestionnaire

Any active distributor member can create a questionnaire. Any active member can participate in a questionnaire.
All the reward deposits are stored at `YALLQuestionnaire` contract.

A questionnaire is backed by the following structs:

```solidity
struct Questionnaire {
	stopped: bool;
	creator: address;
	details: string; // IPLD link
	activeTill: uint256; // Active till timestamp
	deposit: uint256; // YALL Deposit
	reward: uint256; // Reward per member
	submissionCount: uint256;
	mapping (address => Submission) // member => submission;
}

struct Submission {
	submittedAt: uint64; // timestamp
	answers: bytes32[]; // 
}

mapping (uint256 => Questionnaire) // id counter => questionnaire details
```

## Permissions

There are no inbound or outbound permissions defined.

## Interface

### Questionnaire Owner Interface

##### createQuestionnaire(uint256 activeTill, uint256 deposit, uint256 reward, string question)

Any active YALLDistributor member creates a questionnaire. Should approve depositAmount in YALLs first.

##### increaseDeposit(uint256 questionnaireId, uint256 increaseAmount)

A questionnaire owner can increase the deposit if the questionnaire is still active. Should approve increaseAmount in YALLs first.

##### stopQuestionnaire(uint256 questionnaireId)

Stops an non-stopped yet questionnaire if `now() < activeTill`. Once being stopped, a questionnaire can't be re-enabled again.

##### withdrawDeposit(uint256 questionnaireId)

Withdraws an unused deposit in YALLS if a questionnaire if `(stopped && now() < activeTill) || now() > activeTill`. Sends it to a msg.sender.

### Participant Interface

##### submitAnswers(uint256 questionnaireId, bytes32 answers)

* Any active can member submit answers to the questionnaire.
* A member can't submit answers twice for a particular questionnaire.
* A questionnaire rewards in YALLs is transferred to the submitter within the same transaction.
* Answers are stored in the storage.
* There are no checks for a submitted answer array since it resides off-chain.
* Can't submit answers if the questionnaire deposit amount is not enough to pay out a member reward.
