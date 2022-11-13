G1: put subtractions inside of unchecked, if underflow impossible 
a:
in [_transfer](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70), the following 
```
        _balances[from] -= amount; 
        _balances[to] += amount;

```
can be put inside of unchecked
```
        unchecked {
            _balances[from] -= amount;
            _balances[to] += amount;
        }
```

b:
in [withdraw](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L46)
`_balances[msg.sender] -= amount;` can be inside of `unchecked {}` due to prior `require(_balances[msg.sender] >= amount);` 

c:
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574
```
unchecked {
   remainingETH -= price; 
}
```
G2. isInternal and remainingETH can be declared internal without initialization
changing
```
    bool public isInternal = false;
    uint256 public remainingETH = 0;
```
to below:
```
    bool internal isInternal;
    uint256 internal remainingETH;
```

G3. move loop inside of assembly and reuse next_order variables
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L184
change it to something like:
```
assembly {
      let next_order_location := calldataload(add(executions.offset, 0))
      let next_order_pointer := add(executions.offset, next_order_location)

      for {let i := 0} lt(i, executionsLength) {i:=add(i,1)} {

            let memPointer := mload(0x40)

            let order_location := next_order_location
            let order_pointer := next_order_pointer

            let size
            switch eq(add(i, 0x01), executionsLength)
            case 1 {
                size := sub(calldatasize(), order_pointer)
            }
            default {
                next_order_location := calldataload(add(executions.offset, mul(add(i, 0x01), 0x20)))
                next_order_pointer := add(executions.offset, next_order_location)
                size := sub(next_order_pointer, order_pointer)
            }

            mstore(memPointer, 0xe04d94ae00000000000000000000000000000000000000000000000000000000) // _execute
            calldatacopy(add(0x04, memPointer), order_pointer, size)
            // must be put in separate transaction to bypass failed executions
            // must be put in delegatecall to maintain the authorization from the caller
            let result := delegatecall(gas(), address(), memPointer, add(size, 0x04), 0, 0)
        }
   }
```
One can move `uint256 executionsLength = executions.length;` inside of assembly as well

