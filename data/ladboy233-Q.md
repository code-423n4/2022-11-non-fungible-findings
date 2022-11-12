# [L-01] No Use of Upgradeable ReentrancyGuarded && EIP712 Contracts

#### Line Of Code

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L30

#### Vulnerability detail and recommended fix.

Exchange.sol makes use of Open Zeppelin's OwnableUpgradeable.sol && UUPSUpgradeable.sol in the file but does not use an upgradeable version of ReentrancyGuarded.sol && EIP712.sol Contracts.

Consider just using Open Zeppelin's upgradeable equivalent contracts or taking from them what is needed to make your version of the contracts upgradeable.


# [L-02] No Storage Gap for Upgradeable Contracts

#### Line Of Code

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L30

#### Vulnerability detail and recommended fix.

For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable acontract.

If no storage gap is added, when the upgradable contract introduces new variables,
it may override the variables in the inheriting contract.

Consider adding a storage gap at the end of the upgradeable abstract contract

```solidity
uint256[50] private __gap;
```


# [L-03] listingTime can be spoofed in the order, then maker order can be executed as taker and taker order can be treated as maker


#### Line Of Code

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L537

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L251

#### Vulnerability detail and recommended fix.

whether order is taker order or makter order is determined by the listing time, which is in the control of the user,

```solidity
function _canMatchOrders(Order calldata sell, Order calldata buy)
	internal
	view
	returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
{
	bool canMatch;
	if (sell.listingTime <= buy.listingTime) {
		/* Seller is maker. */
		require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
		(canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);
	} else {
		/* Buyer is maker. */
		require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
		(canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);
	}
	require(canMatch, "Orders cannot be matched");

	return (price, tokenId, amount, assetType);
}
```

listingTime can be spoofed in the order, then maker order can be executed as taker and taker order can be treated as maker.

We recommend the exchange assign listing time strictly offline instead of letting user spoof the listing time.

# [L-04] Outdated openzepplein version.

#### Line Of Code

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/package.json#L61

#### Vulnerability detail and recommended fix.

the current openzeppelin version is oudated.

```solidity
"@openzeppelin/contracts": "4.4.1",
"@openzeppelin/contracts-upgradeable": "^4.6.0",
```

Please update the openzepplein package to latest version.


