## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.17",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI

Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

 ## Private/Internal Function Embedded Modifier Reduces Contract Size

Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L40-L46

```
    function _setupExecution() private view {
        remainingETH = msg.value;
        isInternal = true;
        _;
        remainingETH = 0;
        isInternal = false;
    }

    modifier setupExecution() {
        _setupExecution();
        _;
    }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L35-L38
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L48-L51

## `||` Costs Less Gas Than Its Equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

The following three instances entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L371-L379

```
        return (!(
            /* Order must have a trader. */
            (order.trader == address(0)) ||
            /* Order must not be cancelled or filled. */
            (cancelledOrFilled[orderHash]) ||
            /* Order must be settleable. */
            (order.listingTime >= block.timestamp) ||
            (block.timestamp >= order.expirationTime)
        ));
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L412

```
        if (!(order.order.extraParams.length <= 0 || order.order.extraParams[0] != 0x01)) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L572

```
        if (!(msg.sender != buyer || paymentToken != address(0))) {
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. 

The following two instances entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316

```
        nonces[msg.sender] = nonces[msg.sender] + 1;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601

```
            totalFee = totalFee + fee;
```
Similarly, the following instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574

```
            remainingETH = remainingETH - price;
```
## Payable Access Control Functions Cost Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. 

Here are the seven instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56-L66
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L352