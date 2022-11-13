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
```
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
```
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

The following four instances entailed may be refactored as follows:

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
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L62

```
        if (!(msg.sender == EXCHANGE || msg.sender == SWAP)) {
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. 

The following four `+=` instances entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316

```
        nonces[msg.sender] = nonces[msg.sender] + 1;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601

```
            totalFee = totalFee + fee;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36

```
        _balances[msg.sender] = _balances[msg.sender] + msg.value;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74

```
        _balances[to] = _balances[to] + amount;
```

Similarly, the following three `-=` instances entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574

```
            remainingETH = remainingETH - price;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46

```
        _balances[msg.sender] = _balances[msg.sender] - amount;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73

```
        _balances[from] = _balances[from] - amount;
```
## Payable Access Control Functions Cost Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. 

Here are the seven instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56-L66

```
Line 56    function open() external onlyOwner {

Line 66    function _authorizeUpgrade(address) internal override onlyOwner {}
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L352

```
Line 323    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
Line 324        external
Line 325        onlyOwner

Line 332    function setPolicyManager(IPolicyManager _policyManager)
Line 333        external
Line 334        onlyOwner
```

## State Variables Repeatedly Read Should be Cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs.

For instance, `policyManager` could  be cached in the instance below:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L543-L551

```
        address _policyManager = policyManager;

        if (sell.listingTime <= buy.listingTime) {
            /* Seller is maker. */
            require(_policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);
        } else {
            /* Buyer is maker. */
            require(_policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);
        };
```
## Emitted Parameters
Emit a local instead of a state variable whenever possible to save gas. Here are the four instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }

    function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner
    {
        require(address(_policyManager) != address(0), "Address cannot be zero");
        policyManager = _policyManager;
        emit NewPolicyManager(policyManager);
    }

    function setOracle(address _oracle)
        external
        onlyOwner
    {
        require(_oracle != address(0), "Address cannot be zero");
        oracle = _oracle;
        emit NewOracle(oracle);
    }

    function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange);
    }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =). 

Consider replacing `>=` with the strict counterpart `>` in the following three instances:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45

```
        require(_balances[msg.sender] > amount - 1);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71

```
        require(_balances[from] > amount - 1);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573

```
            require(remainingETH > price - 1);
```
Similarly, consider replacing `<=` with the strict counterpart `<` in the following three instances:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L274

```
            sell.order.listingTime < buy.order.listingTime + 1 ? sell.order.trader : buy.order.trader,
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L543

```
        if (sell.listingTime < buy.listingTime + 1) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604

```
        require(totalFee < price + 1, "Total amount of fees are more than the price");
```
