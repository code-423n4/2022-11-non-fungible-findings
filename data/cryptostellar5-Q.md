### N-01 EVENTS MISSING INDEXED FIELDS

*Number of Instances Identified: 5*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
90: event OrdersMatched(address indexed maker,address indexed taker,Order sell,bytes32 sellHash,Order buy,bytes32 buyHash);
99: event OrderCancelled(bytes32 hash);
100: event NonceIncremented(address indexed trader, uint256 newNonce);
105: event NewBlockRange(uint256 blockRange)
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
23: event Transfer(address indexed from, address indexed to, uint256 amount);
```


### N-02 CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
523: require(v == 27 || v == 28, "Invalid v parameter");
```

### N-03 NATSPEC IS INCOMPLETE

This is applicable throughout all the contracts. Well defined @params @dev should be present for each function.

some of the observed instances that do not contain the required NATSPEC are as follows:


```
70: function _transfer(address from, address to, uint256 amount) private {
79: function balanceOf(address user) public view returns (uint256) {
```


