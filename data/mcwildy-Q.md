# Qa Report

## NON-FUNGIBLE

### Use `external` visibility modifier for function that are not being invoked by the contract

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L44:  function withdraw(uint256 amount) public {

line#L58:  function transferFrom(address from, address to, uint256 amount)

line#L79:  function balanceOf(address user) public view returns (uint256) {

line#L83:  function totalSupply() public view returns (uint256) {
```

### Events should use all three `indexed` keywords for their parameters

[Pool.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol):

```solidity
line#L23:  event Transfer(address indexed from, address indexed to, uint256 amount);
```

### Unused import statements

[Exchange.sol](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol):

```solidity
line#L4:   import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```
