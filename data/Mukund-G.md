## USE CUSTOM ERROR INSTEAD OF STRINGS
using custom error instead of strings saves gas
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L36
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L327

## USING BYTES INSTEAD OF STRING DATA TYPES SAVES GAS
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L70
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L71

## USING PRIVATE INSTEAD OF PUBLIC FOR CONSTANTS SAVES GAS
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L70
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L71
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L72
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L73
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L74

## DON'T EXPLICITY INITIALIZE UINT DATA TYPE WITH 0 IT BY DEFAULT START WITH 0
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

## CACHING LENGTH TO A VARIABLE SAVES GAS 
caching array length in a variable saves gas instead or repeatedly checking length
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

## USING UNCHECK BLOCK FOR OPERATION THAT CAN NOT OVEFLOW/UNDERFLOW SAVES GAS
for exapmle using uncheck block for `i++` in loop to saves gas
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

## USING `++i` INSTEAD OF `i++` SAVES GAS
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
