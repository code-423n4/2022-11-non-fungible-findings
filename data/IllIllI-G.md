## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | State variables can be packed into fewer storage slots | 1 | - |
| [G&#x2011;02] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 1 | 113 |
| [G&#x2011;03] | `internal` functions only called once can be inlined to save gas | 6 | 120 |
| [G&#x2011;04] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 | 85 |
| [G&#x2011;05] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 3 | 180 |
| [G&#x2011;06] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 4 | - |
| [G&#x2011;07] | Optimize names to save gas | 1 | 22 |
| [G&#x2011;08] | Functions guaranteed to revert when called by normal users can be marked `payable` | 8 | 168 |

Total: 25 instances over 8 issues with **688 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There is 1 instance of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit Variable ordering with 8 slots instead of the current 9:
///           uint256(32):isOpen, uint256(32):blockRange, mapping(32):cancelledOrFilled, mapping(32):nonces, uint256(32):remainingETH, user-defined(20):executionDelegate, bool(1):isInternal, user-defined(20):policyManager, address(20):oracle
33:       uint256 public isOpen;

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L33

### [G&#x2011;02]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There is 1 instance of this issue:*
```solidity
File: contracts/Exchange.sol

574:              remainingETH -= price;

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574

### [G&#x2011;03]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 6 instances of this issue:*
```solidity
File: contracts/Exchange.sol

440       function _validateUserAuthorization(
441           bytes32 orderHash,
442           address trader,
443           uint8 v,
444           bytes32 r,
445           bytes32 s,
446           SignatureVersion signatureVersion,
447           bytes calldata extraSignature
448:      ) internal view returns (bool) {

471       function _validateOracleAuthorization(
472           bytes32 orderHash,
473           SignatureVersion signatureVersion,
474           bytes calldata extraSignature,
475           uint256 blockNumber
476:      ) internal view returns (bool) {

537       function _canMatchOrders(Order calldata sell, Order calldata buy)
538           internal
539           view
540:          returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)

565       function _executeFundsTransfer(
566           address seller,
567           address buyer,
568           address paymentToken,
569           Fee[] calldata fees,
570:          uint256 price

591       function _transferFees(
592           Fee[] calldata fees,
593           address paymentToken,
594           address from,
595           uint256 price
596:      ) internal returns (uint256) {

653       function _executeTokenTransfer(
654           address collection,
655           address from,
656           address to,
657           uint256 tokenId,
658           uint256 amount,
659:          AssetType assetType

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L440-L448

### [G&#x2011;04]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit require() on line 604
607:          uint256 receiveAmount = price - totalFee;

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607

### [G&#x2011;05]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 3 instances of this issue:*
```solidity
File: contracts/Exchange.sol

184:          for (uint8 i = 0; i < executionsLength; i++) {

307:          for (uint8 i = 0; i < orders.length; i++) {

598:          for (uint8 i = 0; i < fees.length; i++) {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184

### [G&#x2011;06]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 4 instances of this issue:*
```solidity
File: contracts/Exchange.sol

49:           require(isInternal, "This function should not be called directly");

295:          require(!cancelledOrFilled[hash], "Order already cancelled or filled");

604:          require(totalFee <= price, "Total amount of fees are more than the price");

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L49

```solidity
File: contracts/Pool.sol

63:               revert('Caller is not authorized to transfer');

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L63

### [G&#x2011;07]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There is 1 instance of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit open(), close(), initialize(), updateDomainSeparator(), execute(), bulkExecute(), _execute(), cancelOrder(), cancelOrders(), incrementNonce(), setExecutionDelegate(), setPolicyManager(), setOracle(), setBlockRange()
30:   contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L30

### [G&#x2011;08]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 8 instances of this issue:*
```solidity
File: contracts/Exchange.sol

56:       function open() external onlyOwner {

60:       function close() external onlyOwner {

66:       function _authorizeUpgrade(address) internal override onlyOwner {}

323       function setExecutionDelegate(IExecutionDelegate _executionDelegate)
324           external
325:          onlyOwner

332       function setPolicyManager(IPolicyManager _policyManager)
333           external
334:          onlyOwner

341       function setOracle(address _oracle)
342           external
343:          onlyOwner

350       function setBlockRange(uint256 _blockRange)
351           external
352:          onlyOwner

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L56

```solidity
File: contracts/Pool.sol

15:       function _authorizeUpgrade(address) internal override onlyOwner {}

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L15


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 2 | 6 |
| [G&#x2011;02] | Using `bool`s for storage incurs overhead | 2 | 34200 |
| [G&#x2011;03] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 3 | 15 |
| [G&#x2011;04] | Using `private` rather than `public` for constants, saves gas | 3 | - |
| [G&#x2011;05] | Use custom errors rather than `revert()`/`require()` strings to save gas | 19 | - |

Total: 29 instances over 5 issues with **34221 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 2 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit (valid but excluded finding)
307:          for (uint8 i = 0; i < orders.length; i++) {

/// @audit (valid but excluded finding)
598:          for (uint8 i = 0; i < fees.length; i++) {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L307

### [G&#x2011;02]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 2 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit (valid but excluded finding)
85:       mapping(bytes32 => bool) public cancelledOrFilled;

/// @audit (valid but excluded finding)
146:      bool public isInternal = false;

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L85

### [G&#x2011;03]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 3 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit (valid but excluded finding)
184:          for (uint8 i = 0; i < executionsLength; i++) {

/// @audit (valid but excluded finding)
307:          for (uint8 i = 0; i < orders.length; i++) {

/// @audit (valid but excluded finding)
598:          for (uint8 i = 0; i < fees.length; i++) {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184

### [G&#x2011;04]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 3 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit (valid but excluded finding)
70:       string public constant NAME = "Exchange";

/// @audit (valid but excluded finding)
71:       string public constant VERSION = "1.0";

/// @audit (valid but excluded finding)
72:       uint256 public constant INVERSE_BASIS_POINT = 10_000;

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L70

### [G&#x2011;05]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 19 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit (valid but excluded finding)
36:           require(isOpen == 1, "Closed");

/// @audit (valid but excluded finding)
49:           require(isInternal, "This function should not be called directly");

/// @audit (valid but excluded finding)
245:          require(_validateSignatures(sell, sellHash), "Sell failed authorization");

/// @audit (valid but excluded finding)
246:          require(_validateSignatures(buy, buyHash), "Buy failed authorization");

/// @audit (valid but excluded finding)
248:          require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

/// @audit (valid but excluded finding)
249:          require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

/// @audit (valid but excluded finding)
295:          require(!cancelledOrFilled[hash], "Order already cancelled or filled");

/// @audit (valid but excluded finding)
327:          require(address(_executionDelegate) != address(0), "Address cannot be zero");

/// @audit (valid but excluded finding)
336:          require(address(_policyManager) != address(0), "Address cannot be zero");

/// @audit (valid but excluded finding)
345:          require(_oracle != address(0), "Address cannot be zero");

/// @audit (valid but excluded finding)
414:              require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

/// @audit (valid but excluded finding)
523:          require(v == 27 || v == 28, "Invalid v parameter");

/// @audit (valid but excluded finding)
545:              require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

/// @audit (valid but excluded finding)
549:              require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

/// @audit (valid but excluded finding)
552:          require(canMatch, "Orders cannot be matched");

/// @audit (valid but excluded finding)
604:          require(totalFee <= price, "Total amount of fees are more than the price");

/// @audit (valid but excluded finding)
630:              require(to != address(0), "Transfer to zero address");

/// @audit (valid but excluded finding)
632:              require(success, "ETH transfer failed");

/// @audit (valid but excluded finding)
636:              require(success, "Pool transfer failed");

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L36



