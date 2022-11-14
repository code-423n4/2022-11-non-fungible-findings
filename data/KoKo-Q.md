# QA ISSUES FOR [2022-11-NON-FUNGIBLE](https://github.com/code-423n4/2022-11-non-fungible)

## [N-01] Unused import line

./contracts/Exchange.sol

```
L4:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [N-02] Events should have 3 `indexed` fields where possible

./contracts/Pool.sol

```
L23:   event Transfer(address indexed from, address indexed to, uint256 amount);
```

## [N-03] `public` functions not called by the contract should be declared `external` instead

./contracts/Pool.sol

```
L58:   function transferFrom(address from, address to, uint256 amount)

L79:   function balanceOf(address user) public view returns (uint256) {

L83:   function totalSupply() public view returns (uint256) {
```
