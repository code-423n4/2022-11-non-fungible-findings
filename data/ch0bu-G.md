## 1. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs 3 gas

```
49       require(isInternal, "This function should not be called directly");
295      require(!cancelledOrFilled[hash], "Order already cancelled or filled");
604      require(totalFee <= price, "Total amount of fees are more than the price");
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol


## 2. Usage of `uint/int` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.


## 3. Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
66       function _authorizeUpgrade(address) internal override onlyOwner {}
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol
```
15       function _authorizeUpgrade(address) internal override onlyOwner {}
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol


## 4. Using `storage` instead of `memory` for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

```
455	(bytes32[] memory merklePath) = abi.decode(extraSignature, (bytes32[]));
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol


## 6. `public` functions not called by the contract should be declared `external` instead

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from `external` to `public` and can save gas by doing so

```
44	function withdraw(uint256 amount) public {
58	function transferFrom(address from, address to, uint256 amount)
79	function balanceOf(address user) public view returns (uint256) {
83	function totalSupply() public view returns (uint256) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol


## 7. Bytes constants are more efficient than string constants

From the Solidity doc:

* If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

[Why do Solidity examples use bytes32 type instead of string](https://ethereum.stackexchange.com/questions/3795/why-do-solidity-examples-use-bytes32-type-instead-of-string)?

* bytes32 uses less gas because it fits in a single word of the EVM, and string is a dynamically sized-type which has current limitations in Solidity (such as can’t be returned from a function to a contract).

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity. Basically, any fixed size variable in solidity is cheaper than variable size. That will save gas on the contract.

Instances of string constant that can be replaced by bytes(1..32) constant

```
70	string public constant NAME = "Exchange";
71	string public constant VERSION = "1.0";
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol


## 8. Avoid emitting a storage variable when a memory value is available

Here, the values emitted shouldn’t be read from storage. When they are the same, consider emitting the memory value instead of the storage value

```
329	emit NewExecutionDelegate(executionDelegate);
338	emit NewPolicyManager(policyManager);
347	emit NewOracle(oracle);
355	emit NewBlockRange(blockRange);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

