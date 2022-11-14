
# GAS
## Empty blocks should be removed or emit something
### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L66
    function _authorizeUpgrade(address) internal override onlyOwner {}

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L15
    function _authorizeUpgrade(address) internal override onlyOwner {}

### Mitigation
Remove empty blocked or emit something / revert / add logic.


## duplicated `require()` check should be refactored
### Summary
duplicated `require()` / `revert()` checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L327
        require(address(_executionDelegate) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L336
        require(address(_policyManager) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L345
        require(_oracle != address(0), "Address cannot be zero");
### Mitigation
refactor this checks to different functions to save gas


## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks


https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L177
        for (uint8 i=0; i < executionsLength; i++) {

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L184
        for (uint8 i = 0; i < executionsLength; i++) {

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L307
        for (uint8 i = 0; i < orders.length; i++) {

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L598
        for (uint8 i = 0; i < fees.length; i++) {


### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header



## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
fees[i]
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L599-L600

### Mitigation
Cache variables used more than one into a local variable.

## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L56
    function open() external onlyOwner {

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L60
    function close() external onlyOwner {

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L66
    function _authorizeUpgrade(address) internal override onlyOwner {}

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L325
        onlyOwner

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L334
        onlyOwner

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L343
        onlyOwner

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L352
        onlyOwner

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L15
    function _authorizeUpgrade(address) internal override onlyOwner {}
### Mitigation
Consider adding payable to save gas


## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L412
        if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {

### Mitigation
Consider using `>= 1` instead of `> 0` to avoid some opcodes

## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Github Permalinks


https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L316
        nonces[msg.sender] += 1;

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L36
        _balances[msg.sender] += msg.value;

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L74
        _balances[to] += amount;


- Same with `-=`
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L574
            remainingETH -= price;

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L46
        _balances[msg.sender] -= amount;

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L73
        _balances[from] -= amount;



### Mitigation
Don't use `+=` for state variables as it cost more gas.




## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L565
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L653
### Mitigation
Consider changing internal function only called once to inline code for gas savings



## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }
```

`_executionDelegate` parameter of `setExecutionDelegate` can be used directly to avoid unnecessary storage read to save some gas.

Recommendation
Change to:

```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(_executionDelegate);
    }
```

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L315-L356
### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas


