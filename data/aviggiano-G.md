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