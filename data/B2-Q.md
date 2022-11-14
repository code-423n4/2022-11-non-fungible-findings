
## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
```
>File: contracts/Exchange.sol [(line 30)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L30)
```
contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
```
>File: contracts/Pool.sol [(line 13)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L13)

## open `TODO` comments

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
    // TODO: set proper address before deployment
```
* File: contracts/Pool.sol [(line 18)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18)

## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
            (order.listingTime < block.timestamp) &&
            (block.timestamp < order.expirationTime)
```
>File: contracts/Exchange.sol[(line 377-378)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L377-L378)

## `Require` /`revert` should have descriptive reason strings 

```
        require(sell.order.side == Side.Sell);
```
>File: contracts/Exchange.sol [(line 240)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240)

```
        require(msg.sender == order.trader);
```
>File: contracts/Exchange.sol [(line 291)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72

## `NatSpec` is incomplete

```
   /// @audit Missing: '@return'
     * @dev Call the matching policy to check orders can be matched and get execution parameters
     * @param sell sell order
     * @param buy buy order
     */
    function _canMatchOrders(Order calldata sell, Order calldata buy)

```
* File: contracts/Exchange.sol [(line 533-L537)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L533-L537)

## Event is missing `indexed` fields

Each event should use three indexed fields if there are three or more fields.

```
    event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
```
>File: contracts/Exchange.sol [(line 90-96)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L90-L96)

```
   event Transfer(address indexed from, address indexed to, uint256 amount);
```
>File: contracts/Pool.sol [(line 23)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L23)

## Use of ecrecover()`
```
        address recoveredSigner = ecrecover(digest, v, r, s);
```
>File: contracts/Exchange.sol [(line 524)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L524)

## `public` functions not called by the contract should be declared `external` instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

```
    function transferFrom(address from, address to, uint256 amount)
        public
```
* File: contracts/Pool.sol [(line 58-59)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L58-L59
)

```
    function balanceOf(address user) public view returns (uint256) {
```
* File: contracts/Pool.sol [(line 79)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79
)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83


## Unused `receive()` function will lock Ether in contract
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
    receive() external payable {
```

>File: contracts/Pool.sol [(line 28)](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L28)
