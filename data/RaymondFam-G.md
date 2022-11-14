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

Line 60    function close() external onlyOwner {

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

Line 341    function setOracle(address _oracle)
Line 342        external
Line 343        onlyOwner

Line 350    function setBlockRange(uint256 _blockRange)
Line 351        external
Line 352        onlyOwner
```
## State Variables Repeatedly Read Should be Cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs. There are four instances entailed.

`policyManager` in the instance below should be cached as follows:

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
`cancelledOrFilled` in the instance below should be cached as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295-L298

```
        bool _cancelledOrFilled[hash] = cancelledOrFilled[hash];

        require(!_cancelledOrFilled[hash], "Order already cancelled or filled");

        /* Mark order as cancelled, preventing it from being matched. */
        _cancelledOrFilled[hash] = true;
```
`nonces` in the instance below should be cached as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L315-L318

```
    function incrementNonce() external {
        uint256 _nonces[msg.sender] = nonces[msg.sender];

        _nonces[msg.sender] += 1;
        emit NonceIncremented(msg.sender, _nonces[msg.sender]);
    }
```
`isInternal` in the instance below should be cached as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L40-L46

```
    modifier setupExecution() {
        bool _isInternal = isInternal;

        remainingETH = msg.value;
        _isInternal = true;
        _;
        remainingETH = 0;
        _isInternal = false;
    }
```
`remainingETH` in the instance below should be cached as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L572-L575

```
        uint256 _remainingETH = remainingETH;

        if (msg.sender == buyer && paymentToken == address(0)) {
            require(_remainingETH >= price);
            _remainingETH -= price;
        }
```
`_balances` in the two instances below should be cached as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45-L46

```
        uint256 balances[msg.sender] = _balances[msg.sender];

        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71-L74

```
        uint256 balances[msg.sender] = _balances[msg.sender];

        require(balances[from] >= amount);
        require(to != address(0));
        balances[from] -= amount;
        balances[to] += amount;
```
## Unneeded State Variable Cache
The following instance of state variable cache is unnecessary since `remainingETH` is only referenced once in the function call.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L213

```
    function _returnDust() private {
        uint256 _remainingETH = remainingETH;
        assembly {
            if gt(_remainingETH, 0) {
                let callStatus := call(
                    gas(),
                    caller(),
                    selfbalance(),
                    0,
                    0,
                    0,
                    0
                )
            }
        }
    }
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
## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable. 

As an example, the following code block can be refactored as follows:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L591-L609

```
    function _transferFees(
        Fee[] calldata fees,
        address paymentToken,
        address from,
        uint256 price
    ) internal returns (uint256 receiveAmount) {
        uint256 totalFee = 0;
        for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }

        require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        receiveAmount = price - totalFee;
    }
```

All other instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L369
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L390
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L448
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L476
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L522
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L540
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L60
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79-L83

## Ternary Over `if ... else`
Using ternary operator instead of the if else statement saves gas. For instance the following code block may be rewritten as:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L525-L529

```
    recoveredSigner == address(0)
        ? {
            return false;
        }
        : {
           return signer == recoveredSigner;
        }
```
All other instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L543-L551

