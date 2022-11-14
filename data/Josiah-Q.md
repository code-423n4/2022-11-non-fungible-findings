## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logic that depends strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future. Here are the two instances found.

[Exchange.sol#L377-L378](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378)

```
            (order.listingTime < block.timestamp) &&
            (block.timestamp < order.expirationTime)
```
## COMMENT AND CODE LINE LENGTH
Try limit the length of comments and/or code lines to 80 - 100 character long for readability sake. Here is one instance found.

[Exchange.sol#L230](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230)

## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

It is recommended extending the timelock feature to all business critical functions instead of just the contract ownership management. Here are the four instances found.

[Exchange.sol#L323-L356](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356)

```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }

    function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner
    {
        require(address(_policyManager) != address(0), "Address cannot be zero");
        policyManager = _policyManager;
        emit NewPolicyManager(policyManager);
    }

    function setOracle(address _oracle)
        external
        onlyOwner
    {
        require(_oracle != address(0), "Address cannot be zero");
        oracle = _oracle;
        emit NewOracle(oracle);
    }

    function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange);
    }
```
## NEW AND OLD VALUES SHOULD BE EMITTED
It is recommended having events associated with setter functions emit both the new and old values instead of just the new value. Here is one of the instances found.

[Exchange.sol#L323-L356](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356)

## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are some of the instances found.

Missing Natspec in its entirety:

[Exchange.sol#L323-L356](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356)

Missing `* @param amount`:

[Exchange.sol#L645-L667](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L645-L667)

## SANITY CHECKS
Zero address and zero value checks implemented at `initialize()` of `Exchange.sol` could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail `initializer()` preventing the function from getting re-initialized despite all mistakes could always be remedied via the setter functions.

Additionally, it is also recommended adding an optional codehash check for addresses since the zero address check cannot guarantee a matching address has been inputted. Similarly, threshold limits should also be implemented along with zero value check to constitute a more robust engineering design. 

## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users' of needed services. It is recommended setting a threshold for the array length if the array could grow to or entail an excessive size. Here are the three instances found.

[Exchange.sol#L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)

```
        for (uint8 i = 0; i < executionsLength; i++) {
```
[Exchange.sol#L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)

```
        for (uint8 i = 0; i < orders.length; i++) {
```
[Exchange.sol#L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)

```
        for (uint8 i = 0; i < fees.length; i++) {
```
## ECRECOVER SUBJECT TO REPLAY ATTACK
Whenever possible, use OpenZeppelin’s ECDSA library which has been time tested in preventing replay attack of signature malleability associated with `ecrecover'. The issue could arise from the non-unique value of v, and an s value that could end up in the upper half of the modulo set when dealing with the built-in EVM pre-compiled `ecrecover`. This has nonetheless been mitigated with the protocol utilizing nonces, but elsewhere this would be a concern for vulnerability.

Here is one instance found.

[Exchange.sol#L524](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524)

```
        address recoveredSigner = ecrecover(digest, v, r, s);
```
## SINGLE POINT OF FAILURE
Contract owner possesses numerous privileges that could prove disastrous if the private key was compromised. If the key was lost/unrecoverable, all needed system parameter updates would be halted.

An established owner is generally prone to target attack, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure, leading to transfer of ownership and malicious reset of setter function parameters.

The following measures are recommended.
1) Split privileges using multisig option making sure that no one address has excessive ownership of the system.
2) Clearly document the functions and implementations the owner can change.
3) Document the risks associated with privileged users and single points of failure.
4) Make sure that users are aware of all the risks associated with the system.

## STORAGE GAP FOR UPGRADEABLE CONTRACTS
Consider adding a storage gap at the end of each upgradeable contract. In the event some contracts needed to inherit from them, there would not be an issue shifting down of storage in the inheritance chain. Generally, storage gaps are a novel way of reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. If not, the variable in the child contract might be overridden whenever new variables are added to it. This could have unintended and vulnerable consequences to the child contracts.

Here are the two contracts of concern.

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol)
[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol)

## REQUIRE STATEMENT WITH MISSING ERROR MESSAGE
Consider adding a relevant message to all require statements that could be propagated in the event of a revert. 

There are three instances found.

[Exchange.sol#L240](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240)

```
        require(sell.order.side == Side.Sell);
```
[Exchange.sol#L291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291)

```
        require(msg.sender == order.trader);
```
[Exchange.sol#L573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)

```
            require(remainingETH >= price);
```
## CONTRACT EXISTENCE CHECK
Low-level calls do not check for contract existence. They are known to return success even though no function call has been executed when involving contract that may not have been deployed or have been self-destructed. 

Here is one of the instances found.

[Exchange.sol#L631](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L631)

```
            (bool success,) = payable(to).call{value: amount}("");
```
## USE DELETE WHEN SETTING TO DEFAULT VALUES
As documented in the link:

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=delete#delete

It is recommended using `delete` whenever there is a need to set a state variable to its default value, which has a good side effect of saving gas. 

Here are the three instances found.

[Exchange.sol#L44-L45](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L44-L45)

```
        remainingETH = 0;
        isInternal = false;
```
[Exchange.sol#L61](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L61)

```
        isOpen = 0;
```
## USE UINT256 INSTEAD OF 'UINT`
For explicitness reason, it is recommended replacing all instances of `uint` with `uint256`. 

Here is one instance of `uint` used in the code base.

[Exchange.sol#L116](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L116)

```
        uint _blockRange
```
## USE BYTES32 INSTEAD OF 'BYTES`
For explicitness reason, it is recommended replacing all instances of `bytes` with `bytes32`. 

Here is one instance of `bytes` used in the code base.

[Exchange.sol#L447](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L447)

```
        bytes calldata extraSignature
```
## COMPREHENSIVE ZERO ADDRESS CHECK
`_transferTo()` in `Exhange.sol` should have its zero address check move above the if statement on line 628. Doing this will have the check taken care of for all three payment options in the function logic.

Here is the instance found.

[Exchange.sol#L630](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L630)

```
            require(to != address(0), "Transfer to zero address");
```
## TODO
Open TODO signifies an architecture or programming issue needing to be resolved. It is recommended resolving them before deploying.

Here is one instance found.

[Pool.sol#L18](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18)

```
    // TODO: set proper address before deployment
```
## COMMENT AND CODE MISMATCH
The comment instance below stated `Assert`. However, a `require` statement was used in the code line instead.

[Exchange.sol#L290-L291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L290-L291)

```
        /* Assert sender is authorized to cancel order. */
        require(msg.sender == order.trader);
```
## COMMENTED CODE LINES
Code base with lines of code commented out with `//` can lead to confusion and is detrimental to overall code readability. Consider removing commented out lines of code that are no longer needed. 

Here is an instance found.

[Exchange.sol#L282](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L282)

```
        // return (price);
```
## UNCOMMENTED ASSEMBLY BLOCK
Assembly is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code unsafer and more error-prone. 

Here is one instance found.

[Exchange.sol#L214-L226](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L214-L226)

```
        assembly {
            if gt(_remainingETH, 0) {
                let callStatus := call(
                    gas(),
                    caller(),
                    selfbalance(),
                    0,
                    0,
                    0,
                    0
                )
            }
        }
```
## MSG.SENDER WITH MULTIPLE ADDRESSES
In `transferFrom()` of `Pool.sol`, the if statement is comparing `msg.sender` with `EXCHANGE` and `SWAP` whose addresses have been set to the same address. However, this will make the function call always reverting when both `EXCHANGE` and `SWAP` are assigned a different address as indicated in the [TODO](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18).

[Pool.sol#L62](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L62) should therefore be refactored as follows by having `&&` replaced with `||`:

```
        if (msg.sender != EXCHANGE || msg.sender != SWAP) {
```