# QA Report for Non Fungible Trading contest
## Overview
During the audit, 1 low and 8 non-critical issues were found.
№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Missing check for zero address | Low | 1
NC-1 | Order of Functions | Non-Critical | 4
NC-2 | Order of Layout | Non-Critical | 5
NC-3 | Public functions can be external | Non-Critical | 4
NC-4 | Missing NatSpec | Non-Critical | 6
NC-5 | Maximum line length exceeded | Non-Critical | 1
NC-6 | No error message in ```require``` | Non-Critical | 7
NC-7 | Open TODOs | Non-Critical | 1
NC-8 | Commented code | Non-Critical | 1

## Low Risk Findings(1)
### L-1. Missing check for zero address
##### Description
If address(0x0) is set it may cause the contract to revert or work wrong.
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L115

##### Recommendation
Add checks.
#
## Non-Critical Risk Findings(8)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
functions before constructor:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56-L66

public functions between external:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L234-L300

private function before public:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L70

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
events are not before modifiers:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L53-L54
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L90-L105

function and modifiers before state variables:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L35-L51

state variables between functions:
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146-L147

#
### NC-3. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L44
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L58
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83

##### Recommendation
Make public functions external, where possible.
#
### NC-4. Missing NatSpec
##### Description
NatSpec is missing for 6 functions in 2 contracts.
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L60
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L70
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83

##### Recommendation
Add NatSpec for all functions.
#
### NC-5. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.
##### Instances
- (185 symbols) https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230

##### Recommendation
Make the lines shorter.
#
### NC-6. No error message in ```require```
##### Instances
- [```require(sell.order.side == Side.Sell);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240) 
- [```require(msg.sender == order.trader);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291) 
- [```require(remainingETH >= price);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573) 
- [```require(_balances[msg.sender] >= amount);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45) 
- [```require(success);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48) 
- [```require(_balances[from] >= amount);```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71) 
- [```require(to != address(0));```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72) 

##### Recommendation
Add error messages.
#
### NC-7. Open TODOs
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18

##### Recommendation
Resolve issues.
#
### NC-8. Commented code
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L282

##### Recommendation
Delete commented code.