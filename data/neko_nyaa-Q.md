## [L-01] `Pool.totalSupply()` may return wrong value

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83

```solidity=
function totalSupply() public view returns (uint256) {
    return address(this).balance;
}
```

The function `Pool.totalSupply()` simply returns the ETH balance of the Pool contract. It may seem like the way to "wrap" ETH into the contract are calling `deposit()` or through `receive()`, which in turn just calls `deposit()`.

However, one can use `selfdestruct` on an ETH-holding contract, which will forcefully send ETH to the recipient contract without calling any functions. This will cause `Pool.totalSupply()` to return the wrong value, and may lead to accounting issues.

**Recommended mitigation steps:** Consider tracking the total supply separately when a user deposits and withdraws.

## [L-02] `ExecutionDelegate.transferERC20` does not check for contract existence

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/ExecutionDelegate.sol#L127-L130

```solidity=
bytes memory returndata = token.functionCall(data);
if (returndata.length > 0) {
    require(abi.decode(returndata, (bool)), "ERC20 transfer failed");
}
```

If the supposedly-ERC20 address does not contain a contract, then `returndata.length` will be 0, and the check will not be performed at all, causing the transfer to silently succeed without actually transferring any data.

It is worth noting that the token has to be admin-approved first, however it is still good practice to minimize human error (e.g. if admin makes a mistake).

**Recommended mitigation steps:** Consider using Openzeppelin's `SafeERC20`, or implementing a contract check. The contract check can be done during the admin-approval step instead of the transferring step, to save user gas cost.

## [L-03] `Pool.sol`: Missing storage gap in upgradeable contract pattern

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L13

It is recommended to add a storage gap in an ungradeable contract, to avoid collision when upgrading.

Consider adding this line, following the pattern of existing upgradeable contracts in the repo (`lib/EIP712.sol` and `lib/ReentrancyGuarded.sol`).

```solidity=
uint256[50] private __gap;
```

## [N-01] Missing reason string in require

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45

```solidity=
require(_balances[msg.sender] >= amount);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48

```solidity=
(bool success,) = payable(msg.sender).call{value: amount}("");
require(success); // @audit there is no reason string
emit Transfer(address(0), msg.sender, amount);
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71-L72

```solidity=
require(_balances[from] >= amount);
require(to != address(0));
```

It is good practice to state the error for easier investigation when an error occurs. In this case the transfer may revert without a reason string.

Also consider using custom errors, as stated in [GAS-5] of C4udit.
