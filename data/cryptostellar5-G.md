### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 3*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
184: for (uint8 i = 0; i < executionsLength; i++) {
307: for (uint8 i = 0; i < orders.length; i++) {
598: for (uint8 i = 0; i < fees.length; i++) {
```

### G-02 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 6*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
440: function _validateUserAuthorization
471: function _validateOracleAuthorization
537: function _canMatchOrders(Order calldata sell, Order calldata buy)
565: function _executeFundsTransfer
591: function _transferFees
653: function _executeTokenTransfer
```

### G-03 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 7*

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
316: nonces[msg.sender] += 1;
574: remainingETH -= price;
601: totalFee += fee;
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
36: _balances[msg.sender] += msg.value;
46: _balances[msg.sender] -= amount;
73: _balances[from] -= amount;
74: _balances[to] += amount;
```

### G-04 INLINE A MODIFIER THAT IS ONLY USED ONCE

*Number of Instances Identified: 1*

` modifier internalCall()` is only used once for `function _execute` , hence it can be inlined to save gas

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
48: modifier internalCall()
```

### G-05 REQUIRE OR REVERT STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

*Number of Instances Identified: 4*

Each extra chunk of bytes past the original 32 incurs an MSTORE which costs 3 gas.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
49: require(isInternal, "This function should not be called directly");
295: require(!cancelledOrFilled[hash], "Order already cancelled or filled");
604: require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
63: revert('Caller is not authorized to transfer');
```

### G-06 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 1*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
540: returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
```


### G-07 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES INCURS OVERHEAD

*Number of Instances Identified: 3*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
184: for (uint8 i = 0; i < executionsLength; i++) {
307: for (uint8 i = 0; i < orders.length; i++) {
598: for (uint8 i = 0; i < fees.length; i++) {
```