
## G-01 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 6 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

440: function _validateUserAuthorization(
471: function _validateOracleAuthorization(
537: function _canMatchOrders(Order calldata sell, Order calldata buy)
565: function _executeFundsTransfer(
591: function _transferFees(
653: function _executeTokenTransfer(
```

-----------

## G-02 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

316: nonces[msg.sender] += 1;
574: remainingETH -= price;
601: totalFee += fee;
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
File: contracts/Pool.sol

36: _balances[msg.sender] += msg.value;
46: _balances[msg.sender] -= amount;
73: _balances[from] -= amount;
74: _balances[to] += amount;
```

-----------

## G-03 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

_There are 6 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

184: for (uint8 i = 0; i < executionsLength; i++) {
307: for (uint8 i = 0; i < orders.length; i++) {
443: uint8 v,
479: uint8 v; bytes32 r; bytes32 s;
519: uint8 v,
598: for (uint8 i = 0; i < fees.length; i++) {
```

----------

## G-04 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

184: for (uint8 i = 0; i < executionsLength; i++) {
307: for (uint8 i = 0; i < orders.length; i++) {
598: for (uint8 i = 0; i < fees.length; i++) {
```

----------------

## G-05 REQUIRE() OR REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

49: require(isInternal, "This function should not be called directly");
295: require(!cancelledOrFilled[hash], "Order already cancelled or filled");
604: require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
File: contracts/Pool.sol

63: revert('Caller is not authorized to transfer');
```

----

## G-06 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

537-540: function _canMatchOrders(Order calldata sell, Order calldata buy)
        internal
        view
        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
```

------
