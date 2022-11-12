
### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::128 => executionDelegate = _executionDelegate;
2022-11-non-fungible/contracts/Exchange.sol::129 => policyManager = _policyManager;
2022-11-non-fungible/contracts/Exchange.sol::130 => oracle = _oracle;
2022-11-non-fungible/contracts/Exchange.sol::131 => blockRange = _blockRange;
2022-11-non-fungible/contracts/Exchange.sol::328 => executionDelegate = _executionDelegate;
2022-11-non-fungible/contracts/Exchange.sol::337 => policyManager = _policyManager;
2022-11-non-fungible/contracts/Exchange.sol::346 => oracle = _oracle;
2022-11-non-fungible/contracts/Exchange.sol::354 => blockRange = _blockRange;
2022-11-non-fungible/contracts/Exchange.sol::452 => hashToSign = _hashToSign(orderHash);
```
#### Tools used
[c4udit](https://github.com/Bnke0x0/c4udit)

### [L02] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::112 => function initialize(
2022-11-non-fungible/contracts/Exchange.sol::117 => ) external initializer {
```
#### Tools used
[c4udit](https://github.com/Bnke0x0/c4udit)

### [L03] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::30 => contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
2022-11-non-fungible/contracts/Pool.sol::13 => contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
```





### Non-Critical Issues:-




### [N01] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::240 => require(sell.order.side == Side.Sell);
2022-11-non-fungible/contracts/Exchange.sol::291 => require(msg.sender == order.trader);
2022-11-non-fungible/contracts/Exchange.sol::573 => require(remainingETH >= price);
2022-11-non-fungible/contracts/Pool.sol::45 => require(_balances[msg.sender] >= amount);
2022-11-non-fungible/contracts/Pool.sol::48 => require(success);
2022-11-non-fungible/contracts/Pool.sol::63 => revert('Caller is not authorized to transfer');
2022-11-non-fungible/contracts/Pool.sol::71 => require(_balances[from] >= amount);
2022-11-non-fungible/contracts/Pool.sol::72 => require(to != address(0));
```
#### Tools used
[c4udit](https://github.com/Bnke0x0/c4udit)

### [N02] constants should be defined rather than using magic numbers


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::202 => mstore(memPointer, 0xe04d94ae00000000000000000000000000000000000000000000000000000000) // _execute
```




### [N03] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

#### Findings:
```
2022-11-non-fungible/contracts/Pool.sol::18 => // TODO: set proper address before deployment
```


### [N04] Unused file


#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::1 => // SPDX-License-Identifier: MIT
2022-11-non-fungible/contracts/Pool.sol::1 => // SPDX-License-Identifier: MIT
```



### [N05] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::289 => function cancelOrder(Order calldata order) public {
2022-11-non-fungible/contracts/Pool.sol::35 => function deposit() public payable {
2022-11-non-fungible/contracts/Pool.sol::44 => function withdraw(uint256 amount) public {
2022-11-non-fungible/contracts/Pool.sol::79 => function balanceOf(address user) public view returns (uint256) {
2022-11-non-fungible/contracts/Pool.sol::83 => function totalSupply() public view returns (uint256) {
```

