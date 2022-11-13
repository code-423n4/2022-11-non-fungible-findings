## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Using Private Rather Than Public For Constants, Saves Gas | 5 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Empty Blocks Should Be Removed Or Emit Something | 2 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 3 | 105 |
| [GAS&#x2011;4](#GAS&#x2011;4) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 3 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Use assembly to check for `address(0)` | 1 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Public Functions To External | 5 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 2 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Optimize names to save gas | 2 | 44 |
| [GAS&#x2011;9](#GAS&#x2011;9) | Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states | 8 | 40000 |
| [GAS&#x2011;10](#GAS&#x2011;10) | Using fixed bytes is cheaper than using `string` | 2 | - |

Total: 33 contexts over 10 issues

## Gas Optimizations


### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Using Private Rather Than Public For Constants, Saves Gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

#### <ins>Proof Of Concept</ins>


```
70: string public constant NAME = "Exchange";
71: string public constant VERSION = "1.0";
72: uint256 public constant INVERSE_BASIS_POINT = 10_000;
73: address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
74: address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L70-L74



#### <ins>Recommended Mitigation Steps</ins>

Set variable to private.






### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```
66: function _authorizeUpgrade(address) internal override onlyOwner {}
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L66

```
15: function _authorizeUpgrade(address) internal override onlyOwner {}
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L15






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```
184: for (uint8 i = 0; i < executionsLength; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L184

```
307: for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L307

```
598: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L598





### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```
295: require(!cancelledOrFilled[hash], "Order already cancelled or filled");
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L295

```
414: require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L414

```
604: require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L604






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}
```

#### <ins>Proof Of Concept</ins>


```
function setOracle(address _oracle)
        external
        onlyOwner
    {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L341






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```
function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L289

```
function deposit() public payable {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L35

```
function withdraw(uint256 amount) public {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L44

```
function balanceOf(address user) public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L79

```
function totalSupply() public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L83






### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```
479: uint8 v;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L479

```
499: uint8 _v, bytes32 _r, bytes32 _s;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L499




### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

```
File: \2022-11-non-fungible\contracts\Exchange.sol
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol

```
File: \2022-11-non-fungible\contracts\Pool.sol
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from `true` to `false` can cost up to ~20000 gas rather than `uint256(2)` to `uint256(1)` that would cost significantly less.

#### <ins>Proof Of Concept</ins>


```
42: isInternal = true;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L42

```
45: isInternal = false;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L45

```
146: isInternal = false;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L146

```
85: mapping(bytes32 => bool) public cancelledOrFilled;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L85

```
146: bool public isInternal = false;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L146

```
254: cancelledOrFilled[sellHash] = true;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L254

```
255: cancelledOrFilled[buyHash] = true;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L255

```
298: cancelledOrFilled[hash] = true;
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L298






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```
70: string public constant NAME = "Exchange";
71: string public constant VERSION = "1.0";
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L70-L71






