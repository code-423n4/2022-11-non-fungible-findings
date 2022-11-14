### [L-01] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
contracts/Exchange.sol:L524        address recoveredSigner = ecrecover(digest, v, r, s);

```

### [L-02] ```require()```/```revert()``` statements should have descriptive strings.


#### Impact
Consider adding descriptive strings in ```require()```/```revert()```.


#### Findings:
```
contracts/Exchange.sol:L240        require(sell.order.side == Side.Sell);

contracts/Exchange.sol:L291        require(msg.sender == order.trader);

contracts/Exchange.sol:L573            require(remainingETH >= price);

contracts/Pool.sol:L45        require(_balances[msg.sender] >= amount);

contracts/Pool.sol:L48        require(success);

contracts/Pool.sol:L71        require(_balances[from] >= amount);

contracts/Pool.sol:L72        require(to != address(0));

```

### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
contracts/Pool.sol:L18    // TODO: set proper address before deployment

```

