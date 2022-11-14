# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Front-runable `initialize` function | Low | 1 |
| 2      | Setters should check the input value and revert if it's the zero address or zero | Low | 1 |
| 3      | `require()`/`revert()` statements should have descriptive reason strings | NC | 6 |
| 4      | Named return variables not used anywhere in the functions | NC | 1 |

## Findings

### 1- Front-runable `initialize` function :

The `initialize` function inside the Exchange contract is used to initialize the contract owner and important contract parameters (executionDelegate, policyManager, oracle), but any attacker can initialize the contract before the legitimate deployer and even if the developers when deploying call immediately the `initialize` function, malicious agents can trace the protocol deployment transactions and insert their own transaction between them and by doing so they front run the developers call and gain the ownership of the contract and set the wrong parameters.

The impact of this issue is : 

* In the best case developers notice the problem and have to redeploy the contract and thus costing more gas.

* In the worst case the protocol continue to work with the wrong owner and state parameters which could lead to the loss of user funds.

#### Risk : Low 

#### Proof of Concept

Instances include:

File: contracts/Exchange.sol [Line 112-117](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L112-L117)

```
function initialize(
    IExecutionDelegate _executionDelegate,
    IPolicyManager _policyManager,
    address _oracle,
    uint _blockRange
) external initializer
```

#### Mitigation

It's recommended to use the constructor to initialize non-proxied contracts.

For initializing proxy (upgradable) contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify that the transaction succeeded.

### 2- Setters should check the input value and revert if it's the zero address or zero  :

When setting a new value to a state variable the setter function must check the new value and revert if it's the zero address or zero..

#### Risk : Low

#### Proof of Concept

Instances include:

File: contracts/Exchange.sol [Line 350-352](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350-L352)

```
function setBlockRange(uint256 _blockRange) external onlyOwner
```

#### Mitigation
Add non-zero address/uint checks in the setters for the instances aforementioned.

### 3- `require()`/`revert()` statements should have descriptive reason strings :

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/Exchange.sol

[require(sell.order.side == Side.Sell);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240)

[require(msg.sender == order.trader);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291)

[require(remainingETH >= price);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573)

File: contracts/Pool.sol

[require(_balances[msg.sender] >= amount);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45)

[require(_balances[from] >= amount);](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71)

[require(to != address(0));](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72)

#### Mitigation
Add reason strings to the aforementioned require statements for better comprehension.

### 4- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/Exchange.sol

[returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L540)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.