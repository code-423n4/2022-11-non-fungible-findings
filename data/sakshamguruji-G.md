## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

### Description:

Using the addition operator instead of plus-equals saves 113 gas

Affected Code:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601


## EVENTHOUGH THE KNOWN ISSUES STATES THE ISSUE "Don't initialize variables with default value" IT MISSED ONE INSTANCE

### Description:

The line https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146 initialises a bool to false , 
eventhough bools are false by default.
