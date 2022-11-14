
## Summary

- G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 4 instances
- G-02 internal functions only called once can be inlined to save gas | 10 instances
- G-03 Empty blocks should be removed or emit something | 2 instances
- G-04 Functions guaranteed to revert when called by normal users can be marked payable | 7 instances

Total: 23 instances in 4 issues.

---


## G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instances (4):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601

Pool.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74



## G-02 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Instances (10):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L366
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L387
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L440
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L471
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L516
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L537
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L565
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L591
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L618
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L653



## G-03 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

Instances (2):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66

Pool.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15



## G-04 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

Instances (7):

Exchange.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350

Pool.sol
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15

