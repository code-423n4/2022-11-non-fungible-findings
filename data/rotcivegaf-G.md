# Gas report

## Author: rotcivegaf

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | Use `uint256(1)` and `uint256(2)` for `0`(false)/`1`(`true`) to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from `0`(false) to `1`(`true`), after having been `1`(`true`) in the past, [see source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) | 1 |
| [G&#x2011;02] | `public` functions to `external` functions | 4 |
| [G&#x2011;03] | Using `private` rather than `public` for constants, saves gas | 2 |
| [G&#x2011;04] | Unnecessary `require` | 6 |
| [G&#x2011;05] | `<x> += <y>`/`<x> -= <y>` costs more gas than `<x> = <x> + <y>`/`<x> = <x> - <y>` for state variables | 6 |


## [G-01] Use `uint256(1)` and `uint256(2)` for `0`(false)/`1`(`true`) to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from `0`(false) to `1`(`true`), after having been `1`(`true`) in the past, [see source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

Recommended Mitigation: Change `0` to `1` and `1` to `2`

```solidity
File: contracts/Exchange.sol

 33    uint256 public isOpen;

 36        require(isOpen == 1, "Closed");

 57        isOpen = 1;

 61        isOpen = 0;

119        isOpen = 1;
```

## [G-02] `public` functions to `external` functions

The following functions could be set external to save gas and improve code quality
`external` call cost is less expensive than of `public` functions

```solidity
File: contracts/Pool.sol

44    function withdraw(uint256 amount) public {

59        public

79    function balanceOf(address user) public view returns (uint256) {

83    function totalSupply() public view returns (uint256) {
```

## [G-03] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

```solidity
File: contracts/Exchange.sol

146    bool public isInternal = false;

147    uint256 public remainingETH = 0;
```

## [G-04] Unnecessary `require`

With `a - b` if `a` is lower than `b` this revert with `panic code 0x11 (Arithmetic operation underflowed or overflowed outside of an unchecked block)`
Can remove `require` or use `unchecked` in the line

```solidity
File: contracts/Exchange.sol

/// @audit: checked in L573
573            require(remainingETH >= price);
574            remainingETH -= price;

/// @audit: checked in L604
604        require(totalFee <= price, "Total amount of fees are more than the price");
605
606        /* Amount that will be received by seller. */
607        uint256 receiveAmount = price - totalFee;
```

```solidity
File: contracts/Pool.sol

/// @audit: can't overflow
36        _balances[msg.sender] += msg.value;

/// @audit: checked in L45
45        require(_balances[msg.sender] >= amount);
46        _balances[msg.sender] -= amount;

/// @audit: checked in L71
71        require(_balances[from] >= amount);
72        require(to != address(0));
73        _balances[from] -= amount;

/// @audit: can't overflow
74        _balances[to] += amount;
```

## [G-05] `<x> += <y>`/`<x> -= <y>` costs more gas than `<x> = <x> + <y>`/`<x> = <x> - <y>` for state variables

```solidity
File: contracts/Exchange.sol

316        nonces[msg.sender] += 1;

574            remainingETH -= price;
```

```solidity
File: contracts/Pool.sol

37        _balances[msg.sender] += msg.value;

48        _balances[msg.sender] -= amount;

76        _balances[from] -= amount;

77        _balances[to] += amount;
```
