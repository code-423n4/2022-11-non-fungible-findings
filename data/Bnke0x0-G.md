

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::33 => uint256 public isOpen;
2022-11-non-fungible/contracts/Exchange.sol::78 => IExecutionDelegate public executionDelegate;
2022-11-non-fungible/contracts/Exchange.sol::79 => IPolicyManager public policyManager;
2022-11-non-fungible/contracts/Exchange.sol::80 => address public oracle;
2022-11-non-fungible/contracts/Exchange.sol::81 => uint256 public blockRange;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::80 => address public oracle;
```




### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::307 => for (uint8 i = 0; i < orders.length; i++) {
2022-11-non-fungible/contracts/Exchange.sol::598 => for (uint8 i = 0; i < fees.length; i++) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::177 => for (uint8 i=0; i < executionsLength; i++) {
2022-11-non-fungible/contracts/Exchange.sol::184 => for (uint8 i = 0; i < executionsLength; i++) {
2022-11-non-fungible/contracts/Exchange.sol::307 => for (uint8 i = 0; i < orders.length; i++) {
2022-11-non-fungible/contracts/Exchange.sol::598 => for (uint8 i = 0; i < fees.length; i++) {
```



### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::49 => require(isInternal, "This function should not be called directly");
2022-11-non-fungible/contracts/Exchange.sol::295 => require(!cancelledOrFilled[hash], "Order already cancelled or filled");
2022-11-non-fungible/contracts/Exchange.sol::604 => require(totalFee <= price, "Total amount of fees are more than the price");
```



### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::461 => return _verify(trader, hashToSign, v, r, s);
2022-11-non-fungible/contracts/Exchange.sol::505 => return _verify(oracle, oracleHash, v, r, s);
2022-11-non-fungible/contracts/Pool.sol::80 => return _balances[user];
```



### [G07] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::412 => if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {
```



### [G08] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::44 => remainingETH = 0;
2022-11-non-fungible/contracts/Exchange.sol::61 => isOpen = 0;
2022-11-non-fungible/contracts/Exchange.sol::147 => uint256 public remainingETH = 0;
2022-11-non-fungible/contracts/Exchange.sol::184 => for (uint8 i = 0; i < executionsLength; i++) {
2022-11-non-fungible/contracts/Exchange.sol::307 => for (uint8 i = 0; i < orders.length; i++) {
2022-11-non-fungible/contracts/Exchange.sol::597 => uint256 totalFee = 0;
2022-11-non-fungible/contracts/Exchange.sol::598 => for (uint8 i = 0; i < fees.length; i++) {
```



### [G09] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::177 => for (uint8 i=0; i < executionsLength; i++) {
2022-11-non-fungible/contracts/Exchange.sol::184 => for (uint8 i = 0; i < executionsLength; i++) {
2022-11-non-fungible/contracts/Exchange.sol::307 => for (uint8 i = 0; i < orders.length; i++) {
2022-11-non-fungible/contracts/Exchange.sol::598 => for (uint8 i = 0; i < fees.length; i++) {
```


### [G10] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::72 => uint256 public constant INVERSE_BASIS_POINT = 10_000;
```

### [G11] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::327 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::336 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::345 => require(_oracle != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::630 => require(to != address(0), "Transfer to zero address");
2022-11-non-fungible/contracts/Pool.sol::72 => require(to != address(0));
```



### [G12] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::36 => require(isOpen == 1, "Closed");
2022-11-non-fungible/contracts/Exchange.sol::49 => require(isInternal, "This function should not be called directly");
2022-11-non-fungible/contracts/Exchange.sol::245 => require(_validateSignatures(sell, sellHash), "Sell failed authorization");
2022-11-non-fungible/contracts/Exchange.sol::246 => require(_validateSignatures(buy, buyHash), "Buy failed authorization");
2022-11-non-fungible/contracts/Exchange.sol::248 => require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
2022-11-non-fungible/contracts/Exchange.sol::249 => require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
2022-11-non-fungible/contracts/Exchange.sol::295 => require(!cancelledOrFilled[hash], "Order already cancelled or filled");
2022-11-non-fungible/contracts/Exchange.sol::327 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::336 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::345 => require(_oracle != address(0), "Address cannot be zero");
2022-11-non-fungible/contracts/Exchange.sol::414 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
2022-11-non-fungible/contracts/Exchange.sol::523 => require(v == 27 || v == 28, "Invalid v parameter");
2022-11-non-fungible/contracts/Exchange.sol::545 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
2022-11-non-fungible/contracts/Exchange.sol::549 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
2022-11-non-fungible/contracts/Exchange.sol::552 => require(canMatch, "Orders cannot be matched");
2022-11-non-fungible/contracts/Exchange.sol::604 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-11-non-fungible/contracts/Exchange.sol::630 => require(to != address(0), "Transfer to zero address");
2022-11-non-fungible/contracts/Exchange.sol::632 => require(success, "ETH transfer failed");
2022-11-non-fungible/contracts/Exchange.sol::636 => require(success, "Pool transfer failed");
2022-11-non-fungible/contracts/Exchange.sol::641 => revert("Invalid payment token");
```



### [G13] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::56 => function open() external onlyOwner {
2022-11-non-fungible/contracts/Exchange.sol::60 => function close() external onlyOwner {
2022-11-non-fungible/contracts/Exchange.sol::66 => function _authorizeUpgrade(address) internal override onlyOwner {}
2022-11-non-fungible/contracts/Pool.sol::15 => function _authorizeUpgrade(address) internal override onlyOwner {}
```




### [G14] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::635 => bool success = IPool(POOL).transferFrom(from, to, amount);
```



### [G15] Remove unused local variable


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::631 => (bool success,) = payable(to).call{value: amount}("");
2022-11-non-fungible/contracts/Pool.sol::47 => (bool success,) = payable(msg.sender).call{value: amount}("");
```



### [G16] Using `bools` for storage incurs overhead


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::85 => mapping(bytes32 => bool) public cancelledOrFilled;
2022-11-non-fungible/contracts/Exchange.sol::146 => bool public isInternal = false;
```


### [G17] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable getter functions for deployment call data, and not adding another entry to the method ID table
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::70 => string public constant NAME = "Exchange";
2022-11-non-fungible/contracts/Exchange.sol::71 => string public constant VERSION = "1.0";
2022-11-non-fungible/contracts/Exchange.sol::72 => uint256 public constant INVERSE_BASIS_POINT = 10_000;
2022-11-non-fungible/contracts/Exchange.sol::73 => address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
2022-11-non-fungible/contracts/Exchange.sol::74 => address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
```


### [G18] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::66 => function _authorizeUpgrade(address) internal override onlyOwner {}
2022-11-non-fungible/contracts/Pool.sol::15 => function _authorizeUpgrade(address) internal override onlyOwner {}
```

