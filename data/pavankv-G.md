1 .use private instead of public for constant variable  :-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L70
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L72
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L73
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L74

2. use ++i instead of i++ to save gas :-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
 
b. INCREMENTS CAN BE UNCHECKED:-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

3. use custom error instead of strings and reduce string size to 32 bytes to save gas:-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L245
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L246
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L248
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L249
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L414
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L523
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L545
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L549
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L552
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L630
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L632
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L636
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L641
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L63

reference:-
https://blog.soliditylang.org/2021/04/21/custom-errors/

4. use x = x+y and x= x-y instead of x+=y, x -=y to save gas :-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601

5 .use unchecked it can overflow and underflow .:-
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607
