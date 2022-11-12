
# Gas


| | issue |
| ----------- | ----------- |
| 1 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate](#1-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct-where-appropriate) |
| 2 | [state variables can be packed into fewer storage slots](#2-state-variables-can-be-packed-into-fewer-storage-slots) |
| 3 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#3-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 4 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#4-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 5 | [not using the named return variables when a function returns, wastes deployment gas](#5-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 6 | [can make the variable outside the loop to save gas](#6-can-make-the-variable-outside-the-loop-to-save-gas) |
| 7 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#7-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 8 | [require()/revert() strings longer than 32 bytes cost extra gas](#8-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 9 | [require() or revert() statements that check input arguments should be at the top of the function](#9-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 10 | [internal functions only called once can be inlined to save gas](#10-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 11 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#11-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 12 | [bytes constants are more efficient than string constants](#12-bytes-constants-are-more-efficient-than-string-constants) |
| 13 | [public functions not called by the contract should be declared external instead](#13-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 14 | [should use arguments instead of state variable](#14-should-use-arguments-instead-of-state-variable) |



## 1. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

`cancelledOrFilled` and `nonces` are both being used in the same functions mostly consider making them a struct instead 
- [Exchange.sol#L85](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L85)
- [Exchange.sol#L86](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L86)

## 2. state variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

consider making this state var near one of the address types (doesnt matter which because its not used in the same functions)
- [Exchange.sol#L146](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146)


## 3. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

- [Exchange.sol#L607](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607)


## 4. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [Exchange.sol#L316](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316)
- [Exchange.sol#L574](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574)


## 5. not using the named return variables when a function returns, wastes deployment gas

- [Exchange.sol#L540](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L540)


## 6. can make the variable outside the loop to save gas

make it outside and only use it inside

- [Exchange.sol#L599](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L599)


## 7. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [Exchange.sol#L184](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184)
- [Exchange.sol#L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
- [Exchange.sol#L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)


## 8. require()/revert() strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

- [Exchange.sol#L49](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L49)
- [Exchange.sol#L295](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295)
- [Exchange.sol#L604](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604)

- [Pool.sol#L63](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L63)


## 9. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

- [Pool.sol#L71-L72](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71-L72)


## 10. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_canMatchOrders`
- [Exchange.sol#L537](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L537)

`_executeFundsTransfer`
- [Exchange.sol#L565](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L565)

`_transferFees`
- [Exchange.sol#L591](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L591)

`_executeTokenTransfer`
- [Exchange.sol#L653](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L653)

`_validateUserAuthorization`
- [Exchange.sol#L440](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L440)

`_validateOracleAuthorization`
- [Exchange.sol#L471](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L471)


## 11. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [Exchange.sol#L84](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L84)
- [Exchange.sol#L307](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307)
- [Exchange.sol#L443](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L443)
- [Exchange.sol#L479](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L479)
- [Exchange.sol#L519](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L519)
- [Exchange.sol#L598](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)


## 12. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [Exchange.sol#L70](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L70)
- [Exchange.sol#L71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L71)


## 13. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

`withdraw`
- [Pool.sol#L44](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L44)

`transferFrom`
- [Pool.sol#L58](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L58)

`balanceOf`
- [Pool.sol#L79](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79)

`totalSupply`
- [Pool.sol#L83](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83)


## 14. should use arguments instead of state variable

This will save near 97 gas

- [Exchange.sol#L329](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L329)
- [Exchange.sol#L338](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L338)
- [Exchange.sol#L347](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L347)
- [Exchange.sol#L355](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L355)

