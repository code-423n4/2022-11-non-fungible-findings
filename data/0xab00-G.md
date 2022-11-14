
### Exchange.sol `isOpen` - storage from zero to non-zero

```
uint256 public isOpen
```

This is used as a boolean, but it is a uint256. 

To save gas: It would make sense to use values 1 and 2, to avoid updating storage from zero to a non zero.
(It is currently set to 0 and 1). 

WhenOpen modifier uses this, in every call to execute and bulkExecute so this would impact most interactions with the smart contract.

Note: "Using bools for storage incurs overhead" was already reported which is basically the same issue, but it did not include this `isOpen` variable

---------------------

### Exchange.sol `remainingETH` setting from zero to non-zero

Every function that uses the setupExecution modifier will set it from a zero value to a non zero value - and then again to a zero value.

If setupExecution instead set it to `remainingETH = msg.value + 1`, and then reset it to `remainingETH = 1`, and updated _returnDust to `uint256 _remainingETH = remainingETH - 1`, it would save the extra costs of turning a zero into non zero value every time.

Drawback though would be it would be easy to forget that it should always have 1  added to it, and introducing this minor gas savings may lead to real bugs being added in the future. I am not sure I'd actually recommend doing this change due to increased chance of bugs in future.

-----------------------

### Exchange.sol `bulkExecute()` - can `pop()` instead of storing to result variable for minor gas saving

`let result := delegatecall(gas(), address(), memPointer, add(size, 0x04), 0, 0)`

can be changed to:

`pop(delegatecall(gas(), address(), memPointer, add(size, 0x04), 0, 0))`

for a minor gas savings. 
(26 gas)

Can also do this for `Exchange.sol`'s `_returnDust()` function (with `callStatus`).
Minor QA issue in this function too: function does not check the successful status of the call().


-----------------------

### Exchange.sol `incrementNonce()` - can cache `nonce[msg.sender]` to save gas

Can cache `nonces[msg.sender]` to save gas.

Current implementation costs 52156 gas the first time, and 32491 for subsequent calls.
If changed, it can cost 52041 first call (saving 115 gas),  and 32376 (again saving 115 gas) for all calls after that

```
    function incrementNonce() external {
        uint256 newNonce = nonces[msg.sender] + 1;
        nonces[msg.sender] = newNonce;
        emit NonceIncremented(msg.sender, newNonce);
    }
```

------------------------

### Exchange.sol - save gas by sending `NewExecutionDelegate` event arguments from calldata args in `setExecutionDelegate()`, instead of the newly set storage vars (and in 3 other functions)

`setExecutionDelegate()` takes in calldata args, sets storage values with that calldata value, then emits an event and reads from the storage value. But the storage val is the same as calldata (but more expensive to read from).

Can save 157 gas by avoiding reading from storage by changing last line in the function to `emit NewExecutionDelegate(_executionDelegate)` instead of using the `executionDelegate` from storage.

The previous line writes to storage, so the data is the same (just cheaper to read from call data)

This can also be applied to `setPolicyManager()`, `setOracle()`, `setBlockRange()`` which all read from storage when emitting events instead of just emitting the calldata variable (cheaper to read from). I won't repeat it 3 times as the change is easy to see.



------------------

### Exchange.sol _executeFundsTransfer - wrap in unchecked{} to save gas

`remainingETH -= price;` can be safely wrapped in `unchecked { ... }`, due to the assertion in the previous line 

