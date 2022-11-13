# Non Fungible Trading Gas Optimization Findings
## Summary
The Gas savings are calculated using the `yarn test` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00      | Use values 1 and 2 for `isOpen` instead of 0 and 1 | Exchange.sol | 5 | not calculated | not calculated |
| G-01      | Don't initialize variables with default value (Not in automated findings) | Exchange.sol | 1 | 2440 | 0 |
| G-02      | Use `unchecked{++i}` in loops | Exchange.sol | 3 | not calculated | not calculated |
| G-03      | Use functions instead of modifiers | Exchange.sol | 3 modifiers | not calculated | not calculated |

## [G-00] Use values 1 and 2 for `isOpen` instead of 0 and 1
`isOpen=1` should be used instead of `isOpen=0` and `isOpen=2` should be used instead of `isOpen=1`.  
This is because setting storage to a non-zero value from a zero value costs 20000 Gas and setting storage to zero from a non-zero value refunds only 4800 Gas.  

Leaving `isOpen` at a non-zero value continously is significantly cheaper.  

The exact values of saved Gas depend of course on how often `Exchange.open` and `Exchange.close` are called.  

There are 5 instances of `isOpen` that must be adapted accordingly:  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L33](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L33)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L36](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L36)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L57](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L57)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L61](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L61)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L119](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L119)  

## [G-01] Don't initialize variables with default value (Not in automated findings)
There is 1 instance of a variable that is initialized with its default value and that is not already mentioned in the C4audit automated findings.  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L146](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L146)  

Deployment Gas saved: **2440**  
Method call Gas saved: **0**  

## [G-02] Use `unchecked{++i}` in loops
So far the automated C4audit findings only suggest that you should use `++i` instead of `i++`.  
However it is also safe to use `unchecked{++i}` which saves even more Gas since there are no under-/overflow checks.  

There are 3 instances of this:  
[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L184-L208](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L184-L208)  
Fix:  
```solidity
for (uint8 i = 0; i < executionsLength;) {
    ...
    unchecked{++i};
}
```

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L307-L309](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L307-L309)  
Fix:  
```solidity
for (uint8 i = 0; i < orders.length;) {
    ...
    unchecked{++i};
}
```

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L598-L602](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L598-L602)  
Fix:  
```solidity
for (uint8 i = 0; i < fees.length;) {
    ...
    unchecked{++i};
}
```

## [G-03] Use functions instead of modifiers
Modifiers make code more elegant and readable but cost more Gas than functions.  
Arguably, the additional Gas cost outweighs the readability benefit.  

There are 3 instances where modifiers are defined.  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L35](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L35)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L40](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L40)  

[https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L48](https://github.com/code-423n4/2022-11-non-fungible/blob/e9451f78069999cfd8fdd126b87241eb94e93078/contracts/Exchange.sol#L48)  
