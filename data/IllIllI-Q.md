## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Upgradeable contract not initialized | 3 |
| [L&#x2011;02] | Open TODOs | 1 |

Total: 4 instances over 2 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 2 |
| [N&#x2011;02] | `require()`/`revert()` statements should have descriptive reason strings | 7 |
| [N&#x2011;03] | `public` functions not called by the contract should be declared `external` instead | 4 |
| [N&#x2011;04] | `constant`s should be defined rather than using magic numbers | 16 |
| [N&#x2011;05] | Lines are too long | 1 |
| [N&#x2011;06] | NatSpec is incomplete | 9 |
| [N&#x2011;07] | Event is missing `indexed` fields | 5 |

Total: 44 instances over 7 issues

## Low Risk Issues

### [L&#x2011;01]  Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*There are 3 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit missing __UUPS_init()
30:   contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L30

```solidity
File: contracts/Pool.sol

/// @audit missing __Ownable_init()
/// @audit missing __UUPS_init()
13:   contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L13

### [L&#x2011;02]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There is 1 instance of this issue:*
```solidity
File: contracts/Pool.sol

18:       // TODO: set proper address before deployment

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There are 2 instances of this issue:*
```solidity
File: contracts/Exchange.sol

30:   contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L30

```solidity
File: contracts/Pool.sol

13:   contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L13

### [N&#x2011;02]  `require()`/`revert()` statements should have descriptive reason strings

*There are 7 instances of this issue:*
```solidity
File: contracts/Exchange.sol

240:          require(sell.order.side == Side.Sell);

291:          require(msg.sender == order.trader);

573:              require(remainingETH >= price);

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240

```solidity
File: contracts/Pool.sol

45:           require(_balances[msg.sender] >= amount);

48:           require(success);

71:           require(_balances[from] >= amount);

72:           require(to != address(0));

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45

### [N&#x2011;03]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 4 instances of this issue:*
```solidity
File: contracts/Pool.sol

44:       function withdraw(uint256 amount) public {

58        function transferFrom(address from, address to, uint256 amount)
59            public
60:           returns (bool)

79:       function balanceOf(address user) public view returns (uint256) {

83:       function totalSupply() public view returns (uint256) {

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44

### [N&#x2011;04]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 16 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit 0x40
186:                  let memPointer := mload(0x40)

/// @audit 0x20
188:                  let order_location := calldataload(add(executions.offset, mul(i, 0x20)))

/// @audit 0x01
192:                  switch eq(add(i, 0x01), executionsLength)

/// @audit 0x01
/// @audit 0x20
197:                      let next_order_location := calldataload(add(executions.offset, mul(add(i, 0x01), 0x20)))

/// @audit 0xe04d94ae00000000000000000000000000000000000000000000000000000000
202:                  mstore(memPointer, 0xe04d94ae00000000000000000000000000000000000000000000000000000000) // _execute

/// @audit 0x04
203:                  calldatacopy(add(0x04, memPointer), order_pointer, size)

/// @audit 0x04
206:                  let result := delegatecall(gas(), address(), memPointer, add(size, 0x04), 0, 0)

/// @audit 0x01
412:          if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {

/// @audit 0x20
483:                  r := calldataload(add(extraSignature.offset, 0x20))

/// @audit 0x40
484:                  s := calldataload(add(extraSignature.offset, 0x40))

/// @audit 0x20
493:                  v := calldataload(add(extraSignature.offset, 0x20))

/// @audit 0x40
494:                  r := calldataload(add(extraSignature.offset, 0x40))

/// @audit 0x60
495:                  s := calldataload(add(extraSignature.offset, 0x60))

/// @audit 27
/// @audit 28
523:          require(v == 27 || v == 28, "Invalid v parameter");

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L186

### [N&#x2011;05]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There is 1 instance of this issue:*
```solidity
File: contracts/Exchange.sol

230:       * @dev Match two orders, ensuring validity of the match, and execute all associated state transitions. Protected against reentrancy by a contract-global lock. Must be called internally.

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L230

### [N&#x2011;06]  NatSpec is incomplete

*There are 9 instances of this issue:*
```solidity
File: contracts/Exchange.sol

/// @audit Missing: '@return'
364        * @param orderHash hash of order
365        */
366       function _validateOrderParameters(Order calldata order, bytes32 orderHash)
367           internal
368           view
369:          returns (bool)

/// @audit Missing: '@return'
385        * @param orderHash hash of order
386        */
387       function _validateSignatures(Input calldata order, bytes32 orderHash)
388           internal
389           view
390:          returns (bool)

/// @audit Missing: '@return'
438        * @param extraSignature packed merkle path
439        */
440       function _validateUserAuthorization(
441           bytes32 orderHash,
442           address trader,
443           uint8 v,
444           bytes32 r,
445           bytes32 s,
446           SignatureVersion signatureVersion,
447           bytes calldata extraSignature
448:      ) internal view returns (bool) {

/// @audit Missing: '@return'
469        * @param blockNumber block number used in oracle signature
470        */
471       function _validateOracleAuthorization(
472           bytes32 orderHash,
473           SignatureVersion signatureVersion,
474           bytes calldata extraSignature,
475           uint256 blockNumber
476:      ) internal view returns (bool) {

/// @audit Missing: '@return'
514        * @param s s
515        */
516       function _verify(
517           address signer,
518           bytes32 digest,
519           uint8 v,
520           bytes32 r,
521           bytes32 s
522:      ) internal pure returns (bool) {

/// @audit Missing: '@return'
535        * @param buy buy order
536        */
537       function _canMatchOrders(Order calldata sell, Order calldata buy)
538           internal
539           view
540:          returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)

/// @audit Missing: '@return'
589        * @param price price of token
590        */
591       function _transferFees(
592           Fee[] calldata fees,
593           address paymentToken,
594           address from,
595           uint256 price
596:      ) internal returns (uint256) {

/// @audit Missing: '@param amount'
645       /**
646        * @dev Execute call through delegate proxy
647        * @param collection collection contract address
648        * @param from seller address
649        * @param to buyer address
650        * @param tokenId tokenId
651        * @param assetType asset type of the token
652        */
653       function _executeTokenTransfer(
654           address collection,
655           address from,
656           address to,
657           uint256 tokenId,
658           uint256 amount,
659:          AssetType assetType

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L364-L369

```solidity
File: contracts/Pool.sol

/// @audit Missing: '@return'
56         * @param amount Amount to transfer
57         */
58        function transferFrom(address from, address to, uint256 amount)
59            public
60:           returns (bool)

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L56-L60

### [N&#x2011;07]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 5 instances of this issue:*
```solidity
File: contracts/Exchange.sol

90        event OrdersMatched(
91            address indexed maker,
92            address indexed taker,
93            Order sell,
94            bytes32 sellHash,
95            Order buy,
96            bytes32 buyHash
97:       );

99:       event OrderCancelled(bytes32 hash);

100:      event NonceIncremented(address indexed trader, uint256 newNonce);

105:      event NewBlockRange(uint256 blockRange);

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L90-L97

```solidity
File: contracts/Pool.sol

23:       event Transfer(address indexed from, address indexed to, uint256 amount);

```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23

