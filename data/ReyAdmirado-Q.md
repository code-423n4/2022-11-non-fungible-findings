
# QA

| | issue |
| ----------- | ----------- |
| 1 | [event is missing indexed fields](#) |
| 2 | [constants should be defined rather than using magic numbers](#) |
| 3 | [lines are too long](#) |
| 4 | [open todos](#) |
| 5 | [Code Structure Deviates From Best-Practice](#) |
| 6 | [Constant redefined elsewhere](#) |
| 7 | [require()/revert() statements should have descriptive reason strings or custom error](#7-requirerevert-statements-should-have-descriptive-reason-strings-or-custom-error) |


## 1. event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

- [Exchange.sol#L90](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L90)

- [Pool.sol#L23](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L23)


## 2. constants should be defined rather than using magic numbers 
Even assembly can benefit from using readable constants instead of hex/numeric literals

- [Exchange.sol#L523](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L523)


## 3. lines are too long
Usually lines in source code are limited to 80 characters. Its advised to keep lines lower than 120 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [Exchange.sol#L230](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230)


## 4. open todos
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [Pool.sol#L18](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18)


## 5. Code Structure Deviates From Best-Practice
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

- [Exchange.sol#L](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L)
- [Pool.sol#L](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L)


## 6. Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678

- [Exchange.sol#L70-L74](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L71-L74)

- [Pool.sol#L17-19](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L17-19)


## 7. require()/revert() statements should have descriptive reason strings or custom error

- [Exchange.sol#L240](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240)
- [Exchange.sol#L291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291)
- [Exchange.sol#L573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)

- [Pool.sol#L45](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45)
- [Pool.sol#L48](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48)
- [Pool.sol#L71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71)
- [Pool.sol#L72](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72)
