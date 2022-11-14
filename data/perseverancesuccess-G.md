# Gas Optimization 1 
Can use unchecked to save gas 

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L607
```
require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        uint256 receiveAmount = price - totalFee;

```

Can use unchecked because after the require, the price always >= totalFee 

```
unchecked {
uint256 receiveAmount = price - totalFee;
}

```
# Gas Optimization 2 
Can use unchecked to save gas 

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574
```
require(remainingETH >= price);
            remainingETH -= price;
```

Can use unchecked because after the require, the price remainingETH >= price

```
unchecked {
 remainingETH -= price;
}

```


