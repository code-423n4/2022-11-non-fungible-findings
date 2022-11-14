### Missing check for array length before loop.

Using `uint8` for loop variable reduces maximum length supported to 256. If this length limit is intentional, please consider adding a length check before the loop with an informative error, otherwise it would revert on big arrays as inputs because of uint overflow with very uninformative error, confusing the caller.

2 Instances

```solidity
        uint256 executionsLength = executions.length;
        for (uint8 i = 0; i < executionsLength; i++) {
            assembly {
                let memPointer := mload(0x40)
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L168-L210

```solidity
    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L306-L310


### Setting `remainingETH` to 0 not needed

`remainingETH` is only used accessed during calls to `execute` and `bulkExecute`, so it is set to 0 only to be set to `msg.value` on the next call to one of these functions. Therefore there is no need to set `remainingETH = 0` at the end.

```solidity
    modifier setupExecution() {
        remainingETH = msg.value;
        isInternal = true;
        _;
        remainingETH = 0;
        isInternal = false;
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L40-L46


### `success` check not needed
The return value from `IPool(POOL).transferFrom` will always be `true`, as the function `transferFrom` would revert otherwise. Therefore there is no need for the following check `require(success, "Pool transfer failed")`. Please consider omitting this check.

```solidity
    function _transferTo(
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
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L618-L643

### Missing check for zero address

The `transferFrom` from `WETH` contract does not check for zero address before transfer, so the caller must check this. Please consider adding zero address check to the `paymentToken == WETH` if/else branch.


```solidity
    function _transferTo(
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
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L618-L643

### Add zero address check comment. 
Please consider adding a comment to the `paymentToken == POOL` if/else branch, stating that zero address check is done internally by the `transferFrom` from `Pool`.

```solidity
    function _transferTo(
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
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L618-L643


### Missing custom error
Please consider implementing custom errors for more informative reverts.

2 Instances

```solidity
    function withdraw(uint256 amount) public {
        require(_balances[msg.sender] >= amount);
        _balances[msg.sender] -= amount;
        (bool success,) = payable(msg.sender).call{value: amount}("");
        require(success);
        emit Transfer(address(0), msg.sender, amount);
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50


```solidity
    function _transfer(address from, address to, uint256 amount) private {
        require(_balances[from] >= amount);
        require(to != address(0));
        _balances[from] -= amount;
        _balances[to] += amount;


        emit Transfer(from, to, amount);
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L70-L77

### `Pool.transferFrom` return value is not needed.
`transferFrom` from `Pool` contract will always return `true`, as the function would revert otherwise. Please consider removing this return as this check is not needed at the caller (as described before in this report).

```solidity
    function transferFrom(address from, address to, uint256 amount)
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
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58-L77


### Missing specific events for important functions
Please consider adding specific `Deposit` and `Withdraw` events to `deposit` and `withdraw` functions.

2 instances

```solidity
        function deposit() public payable {
        _balances[msg.sender] += msg.value;
        emit Transfer(msg.sender, address(0), msg.value);
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L35-L38


```solidity
    function withdraw(uint256 amount) public {
        require(_balances[msg.sender] >= amount);
        _balances[msg.sender] -= amount;
        (bool success,) = payable(msg.sender).call{value: amount}("");
        require(success);
        emit Transfer(address(0), msg.sender, amount);
    }
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50