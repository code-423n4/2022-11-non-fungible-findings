## [NAZ-L1] Missing Zero-address Validation
**Severity**: Low
**Context**: [`Exchange.sol#L113-L115`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L113-L115)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L2] Signature Malleability of EVM's `ecrecover()`
**Severity**: Low
**Context**: [`Exchange.sol#L524`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524)

**Description**:
The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible here since the nonce is increased each time, ensuring the signatures are not malleable is considered a best practice.

**Recommendation**:
Use the ecrecover function from [OpenZeppelin's ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) for signature verification. (Ensure using a version `> 4.7.3` for there was a critical bug `>= 4.1.0 < 4.7.3`).


## [NAZ-L3] `DOMAIN_SEPARATOR` Can Change 
**Severity**: Low
**Context**: [`Exchange.sol#L121`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L121)

**Description**:
The variable `DOMAIN_SEPARATOR` is assigned in the constructor and will not change after being initialized. However, if a hard fork happens after the contract deployment, the domain would become invalid on one of the forked chains due to the `block.chainid` has changed.

**Recommendation**:
Consider the solution from [Sushi Trident](https://github.com/sushiswap/trident/blob/concentrated/contracts/pool/concentrated/TridentNFT.sol#L47-L62).


## [NAZ-L4] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`Exchange.sol#L323`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323), [`Exchange.sol#L332`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332), [`Exchange.sol#L341`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341), [`Exchange.sol#L350`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L5] Missing Time locks
**Severity**: Low 
**Context**: [`Exchange.sol#L323`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323), [`Exchange.sol#L332`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332), [`Exchange.sol#L341`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341), [`Exchange.sol#L350`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorised owners have no time for any planned incident response.


## [NAZ-L6] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`Exchange.sol#L112`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L112)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N1] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`Exchange.sol#L66`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66), [`Pool.sol#L15`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N2] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`Pool.sol#L19`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L19)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N3] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`Exchange.sol#L53-L54`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L53-L54), [`Pool.sol#L17-L19`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L17-L19)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N4] TODOs Left In The Code
**Severity**: Informational
**Context**: [`Exchange.sol#L135`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L135),  [`Pool.sol#L18`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N5] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N6] Commented Out Code
**Severity**: Informational
**Context**: [`Exchange.sol#L282`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L282)

**Description**:
There is commented code that makes the code messy and unneeded. 

**Recommendation**:
Consider removing the commented out code.