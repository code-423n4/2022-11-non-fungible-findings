# Non Critical issues

## [NC-01] Exchange \_verify accepts malleable signatures

Should check that ``"s"`` is in the valid range 0 < s < secp256k1n ÷ 2 + 1  before passing it to [ecrecover()](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L524) Not posing an immediate risk in current project since replay protection is obtained by marking the orders as filled by the hash of the signed message rather than the signature itself, but still a good idea to avoid future risks. Add the following check:

```solidity
if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
	return false;
}
```

## [NC-02] Inconsistent transfer recipient validation

The [\_transferTo()](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L618-L643) function allows the trasnfer of ETH, POOL and WETH/ERC20 funds. The code discriminate which one by branching on the paymenToken. In the current project/configuration it means that, the [\_transferTo()](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L618-L643) function directly validates the recipient address against the zero address in the case of transfer of ETHs, while it relies on IPool(POOL)  and executionDelegate contracts to do the check. In this specific case it means that while the Pool contract's transferFrom does the recipient validation against the zero address, the executionDelegate does not.

My suggestion would be to move the ETHs recipient validation to before the paymetToken discrimination, so that the check is consistent for all payment tokens. Also, for consistency, I would add the check to the beginning of [_executeTokenTransfer](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L653).

```solidity
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..498066d 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -616,25 +616,24 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
      * @param amount amount to transfer
      */
     function _transferTo(
         address paymentToken,
         address from,
         address to,
         uint256 amount
     ) internal {
         if (amount == 0) {
             return;
-        }
-
+        }
+        require(to != address(0), "Transfer to zero address");
         if (paymentToken == address(0)) {
-            /* Transfer funds in ETH. */
-            require(to != address(0), "Transfer to zero address");
+            /* Transfer funds in ETH. */
             (bool success,) = payable(to).call{value: amount}("");
             require(success, "ETH transfer failed");
         } else if (paymentToken == POOL) {
             /* Transfer Pool funds. */
             bool success = IPool(POOL).transferFrom(from, to, amount);
             require(success, "Pool transfer failed");
         } else if (paymentToken == WETH) {
             /* Transfer funds in WETH. */
             executionDelegate.transferERC20(WETH, from, to, amount);
         } else {
@@ -651,18 +650,19 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
      * @param assetType asset type of the token
      */
     function _executeTokenTransfer(
         address collection,
         address from,
         address to,
         uint256 tokenId,
         uint256 amount,
         AssetType assetType
     ) internal {
+        require(to != address(0), "Transfer to zero address");
         /* Call execution delegate. */
         if (assetType == AssetType.ERC721) {
             executionDelegate.transferERC721(collection, from, to, tokenId);
         } else if (assetType == AssetType.ERC1155) {
             executionDelegate.transferERC1155(collection, from, to, tokenId, amount);
         }
     }
 }
```