### Report title 
- Using `++i` for incrementing `i` inside of loop should be replaced with `unchecked { ++ }`



### Code Snippet that has not been optimized yet
- Below are lines that has not been optimized yet:
  - https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307-L309
  - https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598-L602



### Explanation
- The `unchecked` block is able to use from solidity version 0.8.0
- The variable `i` inside of loop is not able to overflow because of the condition `i < length`, where length is defined as uint256. The maximum value `i` that can reach is `max(uint)-1` . Therefore, incrementing `i` inside `unchecked` block is safe. Also consuming gas of incrementing `i` inside `unchecked` block is less than using `++i`.



### Recommendation
- Incrementing `i` inside `unchecked` block should be used like below:
   - In case of the first unoptimized-lines (https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307-L309), incrementing `i` inside `unchecked` block could be used like below:
```solidity
    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length;) {
            cancelOrder(orders[i]);

            // Incrementing i inside unchecked block
            unchecked {
                i++
            }
        }
    }
```

   - In case of the second unoptimized-lines (https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598-L602), incrementing `i` inside `unchecked` block could be used like below:
```solidity
        for (uint8 i = 0; i < fees.length;) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
 
            // Incrementing i inside unchecked block
            unchecked {
                i++
            }
       }
```