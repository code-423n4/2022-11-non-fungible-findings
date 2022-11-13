[G-01] Use 1 and 2 to represent 'isInternal' state to save gas
```
(1)
0 => 1 :  cost 22100 gas
1 => 0 :  refund min((21000 + 22100 + ? ) / 5, 19900)
0 => 1 => 0: net cost could be up to about 14000 gas

(2)
1 => 2 : cost 5000 gs
2 => 1 :  refund min((21000 + 5000 + ? ) / 5,  2800)
1 => 2 => 1: net cost 2200 gas
```
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/vm/operations_acl.go#L27
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/state_transition.go#L343
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/params/protocol_params.go#L159
https://github.com/ethereum/go-ethereum/blob/8334b5f51a16b72cf2b3ebdc9e131a442c5289d7/core/state_transition.go#L367