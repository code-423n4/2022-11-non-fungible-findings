# 1. Update `INVERSE_BASIS_POINT` decimals to match 100% and improve code clarity

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..3469260 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -69,7 +69,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     /* Constants */
     string public constant NAME = "Exchange";
     string public constant VERSION = "1.0";
-    uint256 public constant INVERSE_BASIS_POINT = 10_000;
+    uint256 public constant INVERSE_BASIS_POINT = 100_00;
     address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
     address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
 

```