[G-01] Set 1 wei as base value of 'remainingETH' to save gas
```
modifier setupExecution() {
    remainingETH = msg.value + 1;
    // ...
    remainingETH =1;
}
```
Reason:
```
(1)
0 => 1 :  cost 22100 gas
1 => 0 :  refundable += 19900 gas, actual refund gas is based on whole TX, which is minOf(usedGasOfTx / 5, refundable)
0 => 1 => 0: net cost could be up to about 14000 gas

(2)
1 => 2 : cost 5000 gas
2 => 1 :  refundable += 2800 gas, (usdGasOfTx / 5) > ((21000 + 5000 ) / 5) > 2800    // 21000 is IntrinsicGas
1 => 2 => 1: net cost 2200 gas
```
Reference:
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/vm/operations_acl.go#L27
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/state_transition.go#L343
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/params/protocol_params.go#L159
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/state_transition.go#L367


[G-02] Add unchecked {} for subtractions where the operands cannot underflow
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607


[G-03] Use 'uint256' instead of 'uint8' as loop variable
'uint8' has extra overhead than 'uint256'
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L308
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598

[G-04] Public functions not called by the contract should be declared external
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83

[G-05] Reuse local/argument variable instead of state variable
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L329
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L338
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L356

[G-06] Cached state variables in stack to save gas
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L317
