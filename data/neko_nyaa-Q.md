Low risk findings:
1. It is possible for `Pool.totalSupply()` to return wrong value
2. `ExecutionDelegate.transferERC20` does not check for contract existence
3. Misleading/wrong event definition in `Pool.sol`
4. `Pool.sol`: Missing storage gap in upgradeable contract pattern

Non-risk findings:
1. Missing reason string in require
2. Two-step ownership transfer is recommended

## [L-01] It is possible for `Pool.totalSupply()` to return wrong value

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

## [L-03] Misleading/wrong event definition in `Pool.sol`

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L37
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L49
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L76

Let us note the definition of the `Transfer` event:
```solidity=
event Transfer(address indexed from, address indexed to, uint256 amount);
```
Implying that a transfer from "amount" is made by "from" to "to".

When transferring from one user to another:
```solidity=
emit Transfer(from, to, amount);
```
When depositing, the event emitted is:
```solidity=
emit Transfer(msg.sender, address(0), msg.value);
```
When withdrawing:
```solidity=
emit Transfer(address(0), msg.sender, amount);
```

The problem is that, the definition for depositing and withdrawing very misleading for contract readers, especially for those who are familiar and frequently work with ERC20 standard tokens. [According to the EIP-20](https://eips.ethereum.org/EIPS/eip-20#events):
- Minting tokens should emit transfer event from 0 to recipient.
- Burning tokens should emit transfer event from sender to 0.

(Note that the standard does not strictly enforce this, but does make this a guideline)

The pool contract is implemented much like an ERC-20, except with pre-approval, and without being able to approve other addresses. It is not strictly required to conform to ERC-20 standard since this is technically not a token. 

However considering the similarities the `Pool` contract has to ERC-20 and its intended usage, I believe this is a problem for any external developers looking to understand the protocol, as well as performing any operations related to events in general.

**Recommended mitigation steps:** Swap the events for depositing and withdrawing.


## [L-04] `Pool.sol`: Missing storage gap in upgradeable contract pattern

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

## [N-02] Two-step ownership transfer is recommended

Several contracts are marked as `Ownable`, however ownership can be lost due to human error if it is transferred to a non-EOA.

It is recommended to implement a two-step transferring method, where the new owner has to claim ownership.
