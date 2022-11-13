# Report


## QA Report


| |Issue|Instances|
|-|:-|:-:|
| [Low-1] | transferFrom() is able to send Zero amount of token | 1 |
| [Low-2] | Absence of Zero address check, while setting critical and immutable state variable | 3 |
| [Low-3] | Functions don't have any require() to check whether updating value and current value of a state variable in a Onlyowner function same or not | 4 |
| [Low-4] | Return should be a named value instead of a complex serise of && operators | 1 |
| [Low-5] | Consider adding checks for Signature Malleability | 1 |
| [Low-6] | Unsafe ERC20 Operation | 1 |
| [Low-7] | EXCHANGE & SWAP are able to transfer any user funds to other addresses | 1 |


| |Issue|Instances|
|-|:-|:-:|
| [NC-01] | Error Message missing from require() conditions | 7 |
| [NC-02] | Contract don't have any checks for return data value from low level call "call()" | 1 |



### [Low-1] transferFrom() is able to send Zero amount of token
There should be require() that should revert when transferring amount is 0 
*Instances (1)*:
```solidity
File: contracts/Pool.sol

58-68: function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
    {
        if (msg.sender != EXCHANGE && msg.sender != SWAP) {
            revert('Caller is not authorized to transfer');
        }
        _transfer(from, to, amount);

        return true;
    }
```



### [Low-2] Absence of Zero address check when setting important state variable
There should always a zero address check require() in below cases

*Instances (3)*:
```solidity
File: contracts/Exchange.sol

128:        executionDelegate = _executionDelegate;
129:       policyManager = _policyManager;
130:       oracle = _oracle;

```


### [Low-3] Functions don't have any require() to check whether updating value and current value of a state variable in a Onlyowner function same or not

*Instances (4)*:
```solidity
File: contracts/Exchange.sol

328:        executionDelegate = _executionDelegate;
337:        policyManager = _policyManager;
346:        oracle = _oracle;
354:        blockRange = _blockRange;

```

### [Low-4] Return should be a named value instead of a complex serise of && operators

*Instances (1)*:
```solidity
File: contracts/Exchange.sol

371-379:  return (
            /* Order must have a trader. */
            (order.trader != address(0)) &&
            /* Order must not be cancelled or filled. */
            (!cancelledOrFilled[orderHash]) &&
            /* Order must be settleable. */
            (order.listingTime < block.timestamp) &&
            (block.timestamp < order.expirationTime)
        );

```

### [Low-5] Consider adding checks for Signature Malleability
Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

*Instances (1)*:
```solidity
File: contracts/Exchange.sol

524:         address recoveredSigner = ecrecover(digest, v, r, s);

```

### [Low-6] Unsafe ERC20 Operation
ExecutionDegate.sol contract has a function transferERC20 which is used inside function _transferTo() in Exchange.sol contract

As ExecutionDegate.sol contract is out of scope, but its transferERC20 using ERC20 transferFrom() function for transferring erc20 assets, So its recommended to use SafeERC20 instead of ERC20.

*Instances (1)*:
```solidity
File: contracts/Exchange.sol

618-644:     function _transferTo(
        address paymentToken,
        address from,
        address to,
        uint256 amount
    ) internal {
        if (amount == 0) {
            return;
        }

        if (paymentToken == address(0)) {
            /* Transfer funds in ETH. */
            require(to != address(0), "Transfer to zero address");
            (bool success,) = payable(to).call{value: amount}("");
            require(success, "ETH transfer failed");
        } else if (paymentToken == POOL) {
            /* Transfer Pool funds. */
            bool success = IPool(POOL).transferFrom(from, to, amount);
            require(success, "Pool transfer failed");
        } else if (paymentToken == WETH) {
            /* Transfer funds in WETH. */
            executionDelegate.transferERC20(WETH, from, to, amount);
        } else {
            revert("Invalid payment token");
        }
    }        

```
```solidity
File: contracts/ExecutionDelegate.sol
121-131:     function transferERC20(address token, address from, address to, uint256 amount)
        approvedContract
        external
    {
        require(revokedApproval[from] == false, "User has revoked approval");
        bytes memory data = abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, amount);
        bytes memory returndata = token.functionCall(data);
        if (returndata.length > 0) {
          require(abi.decode(returndata, (bool)), "ERC20 transfer failed");
        }
    }
```

### [Low-7] EXCHANGE & SWAP are able to transfer any user funds to other addresses
Via transferFrom() and private function _transfer() EXCHANGE or SWAP able to transfer any user(depositor) fund to other addresses, so these should be trust worthy.

*Instances (1)*:
```solidity
File: contracts/Pool.sol

58-68: function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
    {
        if (msg.sender != EXCHANGE && msg.sender != SWAP) {
            revert('Caller is not authorized to transfer');
        }
        _transfer(from, to, amount);

        return true;
    }

70-77  function _transfer(address from, address to, uint256 amount) private {
        require(_balances[from] >= amount);
        require(to != address(0));
        _balances[from] -= amount;
        _balances[to] += amount;

        emit Transfer(from, to, amount);
    }
```





### [NC-01] Error Message missing from require() conditions
*Instances (7)*:
```solidity
File: contracts/Pool.sol

45:        require(_balances[msg.sender] >= amount);
48:        require(success);
71:        require(_balances[from] >= amount);
72:        require(to != address(0));

```
```solidity
File: contracts/Exchange.sol

240:      require(sell.order.side == Side.Sell);
291:      require(msg.sender == order.trader);
573:      require(remainingETH >= price);
```


### [NC-02] Contract don't have any checks for return data value from low level call "call()"
*Instances (1)*:
```solidity
File: contracts/Pool.sol

47:        (bool success,) = payable(msg.sender).call{value: amount}("");

```