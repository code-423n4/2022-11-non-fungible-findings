
## `abi.encode()` is less efficient than `abi.encodePacked()`

```
        (bytes32[] memory merklePath) = abi.decode(extraSignature, (bytes32[]));
```
>File: contracts/Exchange.sol [(line 455)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L455)


## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


```
    function _validateUserAuthorization(
        bytes32 orderHash,
        address trader,
        uint8 v,
        bytes32 r,
        bytes32 s,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature
    ) internal view returns (bool) {
```
>File: contracts/Exchange.sol [(line 440-448)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L440-L448)

```
    function _validateOracleAuthorization(
        bytes32 orderHash,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature,
        uint256 blockNumber
    ) internal view returns (bool) {
```
>File: contracts/Exchange.sol [(line 471-476)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L471-L476)
```
	Other instacnes of this issue are:
```
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L537-L538
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L565-L571
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L591-L596
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L653-L660


## Unused named `returns`

Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity:

```
        return (price, tokenId, amount, assetType);
```
>File: contracts/Exchange.sol [(line 554)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L554)


## call `emit` from `storage` is more expensive

Emmitting an event using storage data is more expensive

```
        emit NewOracle(oracle);
```
>File: contracts/Exchange.sol [(line 347)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L347)
```
        emit NewPolicyManager(policyManager);
```
>File: contracts/Exchange.sol [(line 338)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L338)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L329
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L355

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

```
        _balances[to] += amount;
```
>File: contracts/Pool.sol [(line 74)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74) 
```
        nonces[msg.sender] += 1;
```
>File: contracts/Exchange.sol [(line 316)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L316) 


## `uint 256` incurs overhead

```
        for (uint8 i = 0; i < executionsLength; i++) {
```
>File: contracts/Exchange.sol[(line 184)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184) 
```
        for (uint8 i = 0; i < orders.length; i++) {
```
>File: contracts/Exchange.sol[(line 184)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307) 
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L443
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L479
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L519
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598
* 