## Reorder the code 
The code in the Exchange.sol looks unorganized. variable and event that are not declare in one place makes the code untidy. Consider to declare the variable first, then the event, modifier, and followed by function.
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

## `public` functions that not called by contract should be declared `external` instead
Contract are allowed to override their parent functions and change the visibility from `external` to `public`. 
There are 2 instances for this issue:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L234-L239
```
    function _execute(Input calldata sell, Input calldata buy)
        public
        payable
        reentrancyGuard
        internalCall
    {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L289
```function cancelOrder(Order calldata order) public {```
