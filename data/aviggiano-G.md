# 1. Cache `_whitelistedPolicies.length()` in `PolicyManager.viewWhitelistedPolicies`

### Code changes

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index bcc7fc9..6d934a0 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -67,9 +67,10 @@ contract PolicyManager is IPolicyManager, Ownable {
         returns (address[] memory, uint256)
     {
         uint256 length = size;
+        uint256 setLength = _whitelistedPolicies.length();
 
-        if (length > _whitelistedPolicies.length() - cursor) {
-            length = _whitelistedPolicies.length() - cursor;
+        if (length > setLength - cursor) {
+            length = setLength - cursor;
         }
 
         address[] memory whitelistedPolicies = new address[](length);

```

### Gas changes

```
# before changes
$ yarn test > gas1

# after changes
$ yarn test > gas2

# get gas changes
$ diff gas1 gas2

20c20
< |  Exchange           ·  execute               ·     252299  ·     307513  ·     273470  ·           24  ·          -  │
---
> |  Exchange           ·  execute               ·     252299  ·     307513  ·     273460  ·           24  ·          -  │
68c68
< |  PolicyManager                               ·          -  ·          -  ·     580496  ·        1.9 %  ·          -  │
---
> |  PolicyManager                               ·          -  ·          -  ·     578756  ·        1.9 %  ·          -  │
```

# 2. Use `uint256` instead of `uint8` on for loops

### Code changes

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..a9447e4 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -595,7 +595,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
+        for (uint256 i = 0; i < fees.length; i++) {
             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
             _transferTo(paymentToken, from, fees[i].recipient, fee);
             totalFee += fee;

```

### Gas changes

```
# before changes
$ yarn test > gas1

# after changes
$ yarn test > gas2

# get gas changes
$ diff gas1 gas2


218c218
< |  Exchange           ·  bulkExecute           ·     796257  ·     902099  ·     852656  ·            6  ·          -  │
---
> |  Exchange           ·  bulkExecute           ·     796105  ·     901909  ·     852492  ·            6  ·          -  │
226c226
< |  Exchange           ·  execute               ·     252299  ·     307525  ·     273464  ·           24  ·          -  │
---
> |  Exchange           ·  execute               ·     252268  ·     307492  ·     273428  ·           24  ·          -  │
280c280
< |  TestExchange                                ·          -  ·          -  ·    3514198  ·       11.7 %  ·          -  │
---
> |  TestExchange                                ·          -  ·          -  ·    3512278  ·       11.7 %  ·          -  │
```