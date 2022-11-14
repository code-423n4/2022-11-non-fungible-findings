### Report title:
- Should use `custom error` statement instead of using `revert("<Error message written by string type>")` statement


<br>

### Code Snippet that has not been optimized yet:
- Below are lines that `revert("<Error message written by string type>")` statement is still used and it has not been optimized yet:
  - https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L641
  - https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L63

<br>

### Description:
- `Custom error` statement that consists of both `error()` statement and `revert()` statement has been able to use since solidity version 0.8.4
   - Using `custom error` statement have been introduced as a more `gas-efficient` way than using `revert("<Error message written by string type>")` statement.
       https://blog.soliditylang.org/2021/04/21/custom-errors/


<br>

### Recommendation:
- Recommend to replace `revert("<Error message written by string type>")` statement with using `custom error` statement that consists of both `error()` statement and `revert()` statement to save gas.
  (Reference：https://blog.soliditylang.org/2021/04/21/custom-errors/ )

For example,
- In case of https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L641 , this should be replaced with using `custom error` statement like below:
```solidity
//@dev - Define a custom error
error InvalidPaymentToken();

contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

    〜〜Omission〜〜

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
            //@dev -Call a custom error defined
            revert InvalidPaymentToken();
        }
    }
```

- In case of https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L63 , this should be replaced with using `custom error` statement like below:
```solidity
//@dev - Define a custom error
error CallerIsNotAuthorizedToTransfer();

contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {

    〜〜Omission〜〜

    function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
    {
        if (msg.sender != EXCHANGE && msg.sender != SWAP) {
            //@dev -Call a custom error defined
            revert CallerIsNotAuthorizedToTransfer();
        }
        _transfer(from, to, amount);

        return true;
    }
```