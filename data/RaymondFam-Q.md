## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of the upgradeable contract, `Exchange.sol`, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain. 

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol

## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here are the four instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323-L356

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378

## Sanity Checks
Zero address and zero value checks should be implemented on `initialize()` of `Exchange.sol`  to avoid accidental error(s) that could result in non-functional calls associated with it. This is of particular concern considering a `initializer()` modifier has been in place preventing the function from getting re-initialized, although the parameters entailed can always be regenerated and/or reassigned via the setter functions.

## Use of `ecrecover` is Susceptible to Signature Malleability
The built-in EVM pre-compiled `ecrecover` is susceptible to signature malleability due to non-unique v and s (which may not always be in the lower half of the modulo set) values, possibly leading to replay attacks. Devoid of the adoption of nonces, this could prove a vulnerability when not carefully used.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524

Consider using OpenZeppelin’s ECDSA library which has been time tested in preventing this malleability where possible.

## Missing Require Error Message
Consider adding a less than 32 character string message to all require statements just so that a relevant message would be displayed in the event of a revert. 

Here are the three instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240

```
        require(sell.order.side == Side.Sell);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291

```
        require(msg.sender == order.trader);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573

```
            require(remainingETH >= price);
```
## Failed Function Call Could Occur Without Contract Existence Check
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed. 

Here is the instance entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L630-L631

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184

```
        for (uint8 i = 0; i < executionsLength; i++) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L307

```
        for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L598

```
        for (uint8 i = 0; i < fees.length; i++) {
```
Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here are some of the instances entailed:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230

