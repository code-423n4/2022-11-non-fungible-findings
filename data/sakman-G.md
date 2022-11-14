# 2022-11-NON-FUNGIBLE

## Low Risk And Non-Critical Issues

### Events not emmited on important state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **1** instances of this issue:_

```solidity
File: contracts/Exchange.sol

112:  function initialize(
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

### `public` functions not called by the contract should be declared `external` instead

_There are **4** instances of this issue:_

```solidity
File: contracts/Pool.sol

44:   function withdraw(uint256 amount) public {

58:   function transferFrom(address from, address to, uint256 amount)

79:   function balanceOf(address user) public view returns (uint256) {

83:   function totalSupply() public view returns (uint256) {
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol

### Not used import

_There are **1** instances of this issue:_

```solidity
File: contracts/Exchange.sol

4:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol
