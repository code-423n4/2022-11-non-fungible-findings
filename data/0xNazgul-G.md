## [NAZ-G1] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`Exchange.sol#L184`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L1847), [`Exchange.sol#L307`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307), [`Exchange.sol#L598`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G2] Use Assembly To Set Storage Variables 
**Context**: [`Exchange.sol#L323`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323), [`Exchange.sol#L332`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332), [`Exchange.sol#L341`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341), [`Exchange.sol#L350`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350)

**Description**:
Using assembly to set storage varaible is cheaper in gas than setting it normally. This also doesn't degrade readability for the code is still self-explanatory EX:
```js
contract SetExpensive {
    uint256 public set;

    // ~22_520 gas
    function setIt(uint256 _set) public {
        set = _set;
    }
}

contract SetOptimized {
    uint256 public set;
    // ~22_512 gas
    function setIt(uint256 _set) public {
        assembly {
            sstore(set.slot, _set)
        }
    }
}
// ~8 gas cheaper
```

**Recommendation**: 
Consider using assembly to set storage varaibles.
