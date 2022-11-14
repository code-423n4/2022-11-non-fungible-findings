## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of the upgradeable contract, `Exchange.sol`, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain. 

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here are the four instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378

## Sanity Checks
Zero address and zero value checks should be implemented on `initialize()` of `Exchange.sol`  to avoid accidental error(s) that could result in non-functional calls associated with it. This is of particular concern considering a `initializer()` modifier has been in place preventing the function from getting re-initialized, although the parameters entailed can always be regenerated and/or reassigned via the setter functions.

## Use of `ecrecover` is Susceptible to Signature Malleability
The built-in EVM pre-compiled `ecrecover` is susceptible to signature malleability due to non-unique v and s (which may not always be in the lower half of the modulo set) values, possibly leading to replay attacks. Devoid of the adoption of nonces, this could prove a vulnerability when not carefully used.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524

Consider using OpenZeppelin’s ECDSA library which has been time tested in preventing this malleability where possible.

## Missing Require Error Message
Consider adding a less than 32 character string message to all require statements just so that a relevant message would be displayed in the event of a revert. 

Here are the three instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240

```
        require(sell.order.side == Side.Sell);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291

```
        require(msg.sender == order.trader);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573

```
            require(remainingETH >= price);
```
## Failed Function Call Could Occur Without Contract Existence Check
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed. 

Here is the instance entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L630-L631

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184

```
        for (uint8 i = 0; i < executionsLength; i++) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

```
        for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

```
        for (uint8 i = 0; i < fees.length; i++) {
```
Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230

## Use `delete` to Clear Variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero and false with delete statements. The instances entailed below could be refactored to:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L44-L45

```
        delete remainingETH;
        delete isInternal;
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

## Use of `uint` Instead of `uint256`
In `Exchange.sol`, there is an instance of uint, as opposed to uint256. In favor of explicitness, consider replacing this instance of uint with uint256.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L116

```
        uint _blockRange
```
## Use of `bytes` Instead of `bytes32`
In `Exchange.sol`, there is an instance of bytes, as opposed to bytes32. In favor of explicitness, consider replacing this instance of bytes with bytes32.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L447

```
        bytes calldata extraSignature
```
## Comment and Code Mismatch
The following comment mentioned `Assert` but the code was a `require` statement.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L290-L291

```
        /* Assert sender is authorized to cancel order. */
        require(msg.sender == order.trader);
```
## Inadequately Commented Assembly Block
Assembly block is used in `Exchange.sol`. While this does not pose a security risk per se, it is at the same time a complicated and critical part of the system. Moreover, as this is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code less safe and more error-prone.

Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L214-L226

## Commented Code
Throughout the codebase `Exchange.sol`, there are lines of code that have been commented out with //. This can lead to confusion and is detrimental to overall code readability. Consider removing commented out lines of code that are no longer needed or replacing them with verbose description.

Here are the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L176-L181
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L282
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L488
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L499-L501

## Contract Owner Has Too Many Privileges
The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner's private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:
1) splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system,
2) clearly documenting the functions and implementations the owner can change,
3) documenting the risks associated with privileged users and single points of failure, and
4) ensuring that users are aware of all the risks associated with the system.

## Add a Timelock to Critical Parameter Change
It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

Consider extending the timelock feature beyond contract ownership management to business critical functions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here is one of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18

```
    // TODO: set proper address before deployment
```
