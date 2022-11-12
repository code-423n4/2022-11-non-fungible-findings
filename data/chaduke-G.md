G1. https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L146
Modifier ``internalcall`` is added to prevent the direct call of function ``_execute()``. However, much gas can be saved if we simply declare function ``_execute()`` as private. This can eliminate the setting and resetting of ``isInternal``, which is very expensive for each execution.

G2. If the implementation of ``isInternal`` is still desired, then code true = 2, and false = 1. since It is much more expensive to change a value from zero to non-zero than from non-zero to non-zero. The modifier ``setupExecution`` should be changed to 
```
 modifier setupExecution() {
        remainingETH = msg.value;
        isInternal = 2;
        _;
        remainingETH = 0;
        isInternal = 1;
    }
```
Modifier ``internalCall()`` will be changed to 
```
modifier internalCall() {
        require(isInternal == 2, "This function should not be called directly");
        _;
    }
```
G3. Similarly, because it is more expensive to change zero to nonzero, we code isOpen in the same way: 2 = true, and 1= false.
We change modifier ``whenopen()`` as follows (functions ``open()`` and ``close()`` can be changed accordingly)
```
modifier whenOpen() {
        require(isOpen == 2, "Closed");
        _;
    }
```

G4. Similarly, the remainingETH value should keep a minium of 1 wei (during ``_returnDust()``, keep 1 wei as the balance always):

```
 modifier setupExecution() {
        remainingETH = msg.value;
        isInternal = 2;
        _;
        remainingETH = 1;
        isInternal = 1;
    }

function _returnDust() private {
        uint256 _remainingETH = remainingETH;
        assembly {
            if gt(_remainingETH, 0) {
                let callStatus := call(
                    gas(),
                    caller(),
                    selfbalance() - 1,
                    0,
                    0,
                    0,
                    0
                )
            }
        }
    }

```

G5: https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70-L77
Check whether amount == 0 to avoid unnecessary zero transfer transaction. 