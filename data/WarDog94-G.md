Description: The check was moved to a modifier and the redundant proxy function was eliminated along with the return that was always true.
```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..3135235 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -632,8 +632,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
             require(success, "ETH transfer failed");
         } else if (paymentToken == POOL) {
             /* Transfer Pool funds. */
-            bool success = IPool(POOL).transferFrom(from, to, amount);
-            require(success, "Pool transfer failed");
+            IPool(POOL).transferFrom(from, to, amount);
         } else if (paymentToken == WETH) {
             /* Transfer funds in WETH. */
             executionDelegate.transferERC20(WETH, from, to, amount);
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..86e70e5 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -22,6 +22,11 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
 
     event Transfer(address indexed from, address indexed to, uint256 amount);
 
+    modifier onlyExchangeOrSwap {
+        require(msg.sender == EXCHANGE || msg.sender == SWAP, 'Caller is not authorized to transfer');
+        _;
+    }
+
     /**
      * @dev receive deposit function
      */
@@ -41,7 +46,7 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      * @dev withdraw ETH from pool
      * @param amount Amount to withdraw
      */
-    function withdraw(uint256 amount) public {
+    function withdraw(uint256 amount) external {
         require(_balances[msg.sender] >= amount);
         _balances[msg.sender] -= amount;
         (bool success,) = payable(msg.sender).call{value: amount}("");
@@ -55,19 +60,7 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      * @param to Pool fund recipient
      * @param amount Amount to transfer
      */
-    function transferFrom(address from, address to, uint256 amount)
-        public
-        returns (bool)
-    {
-        if (msg.sender != EXCHANGE && msg.sender != SWAP) {
-            revert('Caller is not authorized to transfer');
-        }
-        _transfer(from, to, amount);
-
-        return true;
-    }
-
-    function _transfer(address from, address to, uint256 amount) private {
+    function transferFrom(address from, address to, uint256 amount) external onlyExchangeOrSwap {
         require(_balances[from] >= amount);
         require(to != address(0));
         _balances[from] -= amount;
diff --git a/contracts/interfaces/IPool.sol b/contracts/interfaces/IPool.sol
index 43f1666..cebb411 100644
--- a/contracts/interfaces/IPool.sol
+++ b/contracts/interfaces/IPool.sol
@@ -4,7 +4,5 @@ interface IPool {
     function deposit() external payable;
     function withdraw(uint256) external;
 
-    function transferFrom(address, address, uint256)
-        external
-        returns (bool);
+    function transferFrom(address, address, uint256) external;
 }
```
This is the diff report for gas before and after optimization
```diff
diff --git "a/.\\old.txt" "b/.\\new.txt"
index 15f56fe..d9d1743 100644
--- "a/.\\old.txt"
+++ "b/.\\new.txt"
@@ -17,7 +17,7 @@
 ······················|························|·············|·············|·············|···············|··············
 |  Exchange           ·  close                 ·          -  ·          -  ·      29320  ·            3  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  execute               ·     252299  ·     307542  ·     273463  ·           24  ·          -  │
+|  Exchange           ·  execute               ·     252299  ·     307064  ·     273161  ·           24  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  Exchange           ·  incrementNonce        ·      32939  ·      50039  ·      41489  ·            8  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
@@ -67,9 +67,9 @@
 ···············································|·············|·············|·············|···············|··············
 |  PolicyManager                               ·          -  ·          -  ·     580496  ·        1.9 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  Pool                                        ·          -  ·          -  ·     963075  ·        3.2 %  ·          -  │
+|  Pool                                        ·          -  ·          -  ·     955976  ·        3.2 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
 |  StandardPolicyERC721                        ·          -  ·          -  ·     219482  ·        0.7 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  TestExchange                                ·          -  ·          -  ·    3514198  ·       11.7 %  ·          -  │
+|  TestExchange                                ·          -  ·          -  ·    3483494  ·       11.6 %  ·          -  │
 ·----------------------------------------------|-------------|-------------|-------------|---------------|-------------·
 ```