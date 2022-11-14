# Report
## Low Risk ##
### [L-1]: Missing checks for address(0x0)
**Context:**

1. ```oracle = _oracle;``` [L130](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L130) 

**Recommendation:**

Add non-zero address checks when set address state variables.

## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

```return (price, tokenId, amount, assetType);``` [L554](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L554) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Wrong order of functions
**Context:**

1. [Exchange.open](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56) (function must be after constructor)
1. [Exchange.close](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L60) (function must be after constructor)
1. [Exchange._authorizeUpgrade](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66) (function must be after constructor)
1. ```bool public isInternal = false;``` [L146](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L146) (state variable must be before constructor)
1. ```uint256 public remainingETH = 0;``` [L147](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L147) (state variable must be before constructor)
1. [Pool._authorizeUpgrade](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15) (internal function must be after all public functions)
1. ```address private constant EXCHANGE = 0x707531c9999AaeF9232C8FEfBA31FBa4cB78d84a;``` [L17](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L17) (state variable can not go after internal function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-3]: Public functions can be external
**Context:**

1. [Pool.withdraw](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L44) 
1. [Pool.transferFrom](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L58) 
1. [Pool.balanceOf](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79) 
1. [Pool.totalSupply](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83) 

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.

### [N-4]: Require() statements should have descriptive reason string
**Context:**

1. ```require(sell.order.side == Side.Sell);``` [L240](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L240) 
1. ```require(msg.sender == order.trader);``` [L291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L291) 
1. ```require(remainingETH >= price);``` [L573](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L573) 
1. ```require(_balances[msg.sender] >= amount);``` [L45](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L45) 
1. ```require(success);``` [L48](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L48) 
1. ```require(_balances[from] >= amount);``` [L71](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L71) 
1. ```require(to != address(0));``` [L72](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L72) 

### [N-5]: NatSpec is missing
**Context:**

1. ```function open() external onlyOwner {``` [L56](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L56) 
1. ```function close() external onlyOwner {``` [L60](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L60) 
1. ```function _authorizeUpgrade(address) internal override onlyOwner {}``` [L66](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L66) 
1. ```function initialize(``` [L112](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L112) 
1. ```function updateDomainSeparator() external {``` [L135](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L135) 
1. ```function _returnDust() private {``` [L212](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212) 
1. ```function setExecutionDelegate(IExecutionDelegate _executionDelegate)``` [L323](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323) 
1. ```function setPolicyManager(IPolicyManager _policyManager)``` [L332](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332) 
1. ```function setOracle(address _oracle)``` [L341](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341) 
1. ```function setBlockRange(uint256 _blockRange)``` [L350](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350) 
1. ```function _authorizeUpgrade(address) internal override onlyOwner {}``` [L15](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L15) 
1. ```function _transfer(address from, address to, uint256 amount) private {``` [L70](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L70) 
1. ```function balanceOf(address user) public view returns (uint256) {``` [L79](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79) 
1. ```function totalSupply() public view returns (uint256) {``` [L83](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L83) 

### [N-6]: TODO
**Context:**

```// TODO: set proper address before deployment``` [L18](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18) 

### [N-7]: Natspec is incomplete
**Context:**

1. ```function _validateOrderParameters(Order calldata order, bytes32 orderHash)``` [L366](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L366) (return tag is missing)
1. ```function _validateSignatures(Input calldata order, bytes32 orderHash)``` [L387](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L387) (return tag is missing)
1. ```function _validateUserAuthorization(``` [L440](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L440) (return tag is missing)
1. ```function _validateOracleAuthorization(``` [L471](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L471) (return tag is missing)
1. ```function _verify(``` [L516](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L516) (return tag is missing)
1. ```function _canMatchOrders(Order calldata sell, Order calldata buy)``` [L537](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L537) (return tags are missing)
1. ```function _transferFees(``` [L591](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L591) (return tag is missing)
1. ```function transferFrom(address from, address to, uint256 amount)``` [L58](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L58) (return tag is missing)

### [N-8]: Line is too long
**Context:**

1. ```* @dev Match two orders, ensuring validity of the match, and execute all associated state transitions. Protected against reentrancy by a contract-global lock. Must be called internally.``` [L230](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230) 
1. ```* @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order``` [L313](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L313) 
1. ```(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));``` [L500](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L500) 
1. ```(canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);``` [L546](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L546) 
1. ```(canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);``` [L550](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L550) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
