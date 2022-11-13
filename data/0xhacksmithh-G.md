# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1] | Function that guaranteed to revert when called by normal user can be marked payable  | 7 |
| [GAS-2] | <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 7 |
| [GAS-3] | Functions those could be external | 5 |
| [GAS-4] | By Unchecking Arithmetic operations (that never over/underflow) can help in gas saving | 5 |
| [GAS-5] | Use of uint8 for counter in for loop increases gas costs | 3 |


### [GAS-1] Function that guaranteed to revert when called by normal user can be marked payable 
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 

*Instances (7)*:
```solidity
File: contracts/Exchange.sol

56:          function open() external onlyOwner {
60:          function close() external onlyOwner {
66:          function _authorizeUpgrade(address) internal override onlyOwner {}
323:        function setExecutionDelegate(IExecutionDelegate _executionDelegate)
332:        function setPolicyManager(IPolicyManager _policyManager)
341:        function setOracle(address _oracle)
350:        function setBlockRange(uint256 _blockRange)

```


### [GAS-2] <x> += <y> costs more gas than <x> = <x> + <y>(same for "-" also) for state variables

*Instances (7)*:
```solidity
File: contracts/Pool.sol

36:       _balances[msg.sender] += msg.value;
46:       _balances[msg.sender] -= amount;
73:       _balances[from] -= amount;
74:       _balances[to] += amount;

```
```solidity
File: contracts/Exchange.sol

316:       nonces[msg.sender] += 1;
574:       remainingETH -= price;
601:       totalFee += fee;
```


### [GAS-3] Some public function could be external for saving gas

*Instances (5)*:
```solidity
File: contracts/Pool.sol

35:         function deposit() public payable {
44:         function withdraw(uint256 amount) public {
58:         function transferFrom(address from, address to, uint256 amount)
79:         function balanceOf(address user) public view returns (uint256) {
83:         function totalSupply() public view returns (uint256) {

```


### [GAS-4] By Unchecking Arithmetic operations (that never over/underflow) can help in gas saving
```solidity 
for (uint8 i = 0; i < executionsLength; i++) { .... } 
```

should be

```solidity
for (uint8 i = 0; i < executionsLength; ) {
     .....
     unchecked{++i}
}
```

*Instances (5)*:
```solidity
File: contracts/Exchange.sol

184:       for (uint8 i = 0; i < executionsLength; i++) {
307:       for (uint8 i = 0; i < orders.length; i++) {
598:       for (uint8 i = 0; i < fees.length; i++) {
316:       nonces[msg.sender] += 1;
601:       totalFee += fee;

```


### [GAS-5] Use of uint8 for counter in for loop increases gas costs

Due to how the EVM natively works on 256 numbers, using a 8 bit number here introduces additional costs as the EVM has to properly enforce the limits of this smaller type.
*Instances (3)*:
```solidity
File: contracts/Exchange.sol

184:       for (uint8 i = 0; i < executionsLength; i++) {
307:       for (uint8 i = 0; i < orders.length; i++) {
598:       for (uint8 i = 0; i < fees.length; i++) {

```