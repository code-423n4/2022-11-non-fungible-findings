## QA Report - low risk

### Missing checks for address(0x0) when assigning values to address state variables
___
Check for address(0x0) is missing for `_oracle`:

[Exchange.sol: L130](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L130)
```solidity
        oracle = _oracle;
```
___
___


## QA Report: non-critical


###  Outdated Open Zeppelin versions are used
Outdated Open Zeppelin versions (`4.4.1` and `4.6.0`) are used.

The versions used have known vulnerabilties, including three with high severity. See [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories). 
___
___


### Long single-line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are acceptable and a scroll bar is provided, very long comments can interfere with readability. Such a statement can be wrapped using a small indentation in the continuation line (to indicate that the line has wrapped), as shown below:
___
[Exchange.sol: L230](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L230)
```solidity
     * @dev Match two orders, ensuring validity of the match, and execute all associated state transitions. Protected against reentrancy by a contract-global lock. Must be called internally.
```
Suggestion:
```solidity
     * @dev Match two orders, ensuring validity of the match, and execute all associated state transitions.  
     *   Protected against reentrancy by a contract-global lock. Must be called internally.
```
___
[Exchange.sol: L313](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L313)
```solidity
     * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
```
Suggestion:
```solidity
     * @dev Cancel all current orders for a user, preventing them from being matched. 
     *  Must be called by the trader of the order.
```
___
___


### TODO and open item should be resolved
Comments that refer to open items should be addressed
___
[Pool.sol: L18](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18)
```solidity
    // TODO: set proper address before deployment
```
___
[Exchange.sol: L134](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L134)
```solidity
    // temporary function for testing
```
___
___


### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields

___
[Exchange.sol: L90-97](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L90-L97)
```solidity
    event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
    );
```
___
[Pool.sol: L23](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23)
```solidity
    event Transfer(address indexed from, address indexed to, uint256 amount);
```
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___
[Exchange.sol: L543-551](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L543-L551)
```solidity
        if (sell.listingTime <= buy.listingTime) {
            /* Seller is maker. */
            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);
        } else {
            /* Buyer is maker. */
            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);
        }
```
Suggestion: Replace `whitelisted` with `allowlisted`
___
___

### NatSpec statements missing for `constructor` 
NatSpec statements are entirely missing for constructor. In addition, the comment, ` /* Constructor (for ERC1967) */` appears out of place

[Exchange.sol: L107-117](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L107-L117)
```solidity
    constructor() {
      _disableInitializers();
    }


    /* Constructor (for ERC1967) */
    function initialize(
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) external initializer {
```
___
___

### NatSpec statements missing for `event` 
NatSpec statements are entirely missing for all `events`. 

***Example***

[Exchange.sol: L90-97](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L90-L97)
```solidity
    event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
    );
```
Similarly for the remaining events:

[Exchange.sol: L53](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L53)

[Exchange.sol: L54](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L54)

[Exchange.sol: L99](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L99)

[Exchange.sol: L100](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L100)

[Exchange.sol: L102](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L102)

[Exchange.sol: L103](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L103)

[Exchange.sol: L104](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L104)

[Exchange.sol: L105](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L105)

[Pool.sol: L23](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L23)
___
___

### NatSpec statements missing for `function` 
___
The `function` below is missing an `@return` statement

[Pool.sol: L52-60](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L52-L60)
```solidity
    /**
     * @dev transferFrom Transfer balances within pool; only callable by Swap and Exchange
     * @param from Pool fund sender
     * @param to Pool fund recipient
     * @param amount Amount to transfer
     */
    function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
```
___
NatSpec statements are entirely missing for the following  `external` or `public` `functions` (NatSpec is not strictly required for `internal` or `private` functions):

[Exchange.sol: L56-58](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L56-L58)

[Exchange.sol: L60-62](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L60-L62)

[Exchange.sol: L112-117](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L112-L117)

[Exchange.sol: L323-325](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L323-L325)

[Exchange.sol: L332-334](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L332-L334)

[Exchange.sol: L341-343](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L341-L343)

[Exchange.sol: L350-352](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L350-L352)

[Pool.sol: L79-81](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79-L81)

[Pool.sol: L83-85](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83-L85)
___
___

### Missing `require` revert string
A `require` message should be included to enable users to understand the reason for failure 

___
[Exchange.sol: L240](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240)
```solidity
        require(sell.order.side == Side.Sell);
```
___
[Exchange.sol: L291](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L291)
```solidity
        require(msg.sender == order.trader);
```
___
[Exchange.sol: L573](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L573)
```solidity
            require(remainingETH >= price);
```
___
[Pool.sol: L45](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45)
```solidity
        require(_balances[msg.sender] >= amount);
```
___
[Pool.sol: L48](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L48)
```solidity
        require(success);
```
___
[Pool.sol: L71](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L71)
```solidity
        require(_balances[from] >= amount);
```
___
[Pool.sol: L72](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L72)
```solidity
        require(to != address(0));
```
___
___