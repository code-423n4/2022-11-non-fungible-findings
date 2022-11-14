### [G-01] Use assembly to check for zero address.


#### Impact
Save 6 gas when assembly is used to check for zero address.
e.g:
```
assembly {
            if iszero(_addr) {
                mstore(0x00, "zero address")
                revert(0x00, 0x20)
            }
```


#### Findings:
```
contracts/Exchange.sol:L630            require(to != address(0), "Transfer to zero address");

```
### [G-02] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/Exchange.sol:L66    function _authorizeUpgrade(address) internal override onlyOwner {}

contracts/Pool.sol:L15    function _authorizeUpgrade(address) internal override onlyOwner {}

```

### [G-03] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contracts/Exchange.sol:L56    function open() external onlyOwner {

contracts/Exchange.sol:L60    function close() external onlyOwner {

contracts/Exchange.sol:L66    function _authorizeUpgrade(address) internal override onlyOwner {}

contracts/Pool.sol:L15    function _authorizeUpgrade(address) internal override onlyOwner {}

```

### [G-04] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
contracts/Exchange.sol:L574            remainingETH -= price;

contracts/Exchange.sol:L601            totalFee += fee;

```

### [G-05] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
contracts/Exchange.sol:L49        require(isInternal, "This function should not be called directly");

contracts/Exchange.sol:L295        require(!cancelledOrFilled[hash], "Order already cancelled or filled");

contracts/Exchange.sol:L604        require(totalFee <= price, "Total amount of fees are more than the price");

```

### [G-06] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/Exchange.sol:L177        for (uint8 i=0; i < executionsLength; i++) {

contracts/Exchange.sol:L184        for (uint8 i = 0; i < executionsLength; i++) {

contracts/Exchange.sol:L307        for (uint8 i = 0; i < orders.length; i++) {

contracts/Exchange.sol:L598        for (uint8 i = 0; i < fees.length; i++) {

```
