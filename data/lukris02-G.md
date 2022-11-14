# Gas Optimizations Report for Non Fungible Trading contest
## Overview
During the audit, 3 gas issues were found.  
Total savings ~20,245.
№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Optimization for ```isOpen``` | 1 | 20,000
G-2 | Use unchecked blocks for incrementing i | 3 | 105
G-3 | Use unchecked blocks for subtractions where underflow is impossible | 4 | 140

## Gas Optimizations Findings(3)
### G-1. Optimization for ```isOpen```
##### Description
For state variable [```isOpen```](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L33), it is better to use uint256(1) and uint256(2) to avoid spending 20,000 gas when changing from '0' to '1' (when opening after closing).

[Link](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56-L63):
```
    function open() external onlyOwner {
        isOpen = 1;
        emit Opened();
    }
    function close() external onlyOwner {
        isOpen = 0;
        emit Closed();
    }
```

##### Saved
This saves ~20,000.  

#
### G-2. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*3 = 105
#
### G-3. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46
- https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73

##### Saved
This saves ~35.  
So, ~35*4 = 140
#