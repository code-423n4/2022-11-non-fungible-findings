QA1. remove testing function before deployment
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L135

QA2. misplaced comments
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L145

QA3. rounding up fees
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L599
```
uint256 fee = (price * fees[i].rate + 5000) / INVERSE_BASIS_POINT;
```

QA4. unconventional nonreentrant code structure. 
Not clear what is the benefit of setting `_execute` public and nonreentrant. If there is a concern that the code in _execute can cause reentrancy, it would have to go through execute or bulkExecute, which are guarded anyway. Here is how it is guarded in most codebases:
```
function execute(Input calldata sell, Input calldata buy)
        external
        payable
        reentrancyGuard
    {...}
function bulkExecute(Execution[] calldata executions)
        external
        payable
        reentrancyGuard
    {...}
function _execute(Input calldata sell, Input calldata buy)
        internal
    {...}
```