
## N-01 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

```
File: contracts/Exchange.sol

90-97: event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
    );
99: event OrderCancelled(bytes32 hash);
100: event NonceIncremented(address indexed trader, uint256 newNonce);
105: event NewBlockRange(uint256 blockRange);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
File: contracts/Pool.sol

23: event Transfer(address indexed from, address indexed to, uint256 amount);
```

------------

## N-02 OPEN TODOS

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

```
File: contracts/Pool.sol

18: // TODO: set proper address before deployment
```

----------

