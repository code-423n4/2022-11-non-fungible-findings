---

## Summary

- L-01 Missing checks for address(0x0) when assigning values to address state variables
- L-02 ecrecover not checked for zero result

- N-01 Event is missing indexed fields
- N-02 Remove commented out code
- N-03 Open todos


## L-01 Missing checks for address(0x0) when assigning values to address state variables

Instances (4):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L130
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L346
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L131
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L354



## L-02 ecrecover not checked for zero result

A return value of zero indicates an invalid signature, so this is both invalid state-handling and an incorrect message

Instance (1):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524



## N-01 Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L90-L96




## N-02 Remove commented out code

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L174-L182


##  N-03 Open todos
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18

