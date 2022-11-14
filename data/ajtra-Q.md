# Summary
1. L01. Missing checks for address(0x0) when assigning values to address state variables
2. NC01. Declaration of storage variables should be together
3. NC02. TODO comments means it's not a final version
4. NC03. Line Length
5. NC04. Lack of @param  

# Low
## L01 - Missing checks for address(0x0) when assigning values to address state variables

### Mitigation
Add check for address(0x0)

### Lines in the code
[Exchange.sol#L130](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L130)

# Non critical
## NC01. Declaration of storage variables should be together
### Description 
Variable declaration should be together to have a cleaner and more organized code and should be at the top of the contract.

### Mitigation
Move the variables declaration to the top

### Lines in the code
[Exchange.sol#L70-L86](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L70-L86)
[Exchange.sol#L146-L147](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L146-L147)

## NC02. TODO comments means it's not a final version
### Description
There is a TODO comment inside the code what can mean that the code has not been finalished. 
Make sure that the code is finished before deployment in production.

### Lines in the code
[Pool.sol#L18](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18)

## NC03. Line Length
### Description
Max line length must be no more than 120 but many lines are extended past this length.

### Lines in the code
[Exchange.sol#L230](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L230)
[Exchange.sol#L313](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L313)
[Exchange.sol#L500](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L500)
[Exchange.sol#L546](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L546)
[Exchange.sol#L550](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L550)

## NC04. Lack of @param  
### Description
NatSpec is incomplete in many functions. Add @param in the following functions.

### Lines in the code
[Exchange.sol#L112](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L112)
[Exchange.sol#L323](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L323)
[Exchange.sol#L332](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L332)
[Exchange.sol#L341](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L341)
[Exchange.sol#L350](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L350)
[Pool.sol#L70](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70)
[Pool.sol#L79](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79)
