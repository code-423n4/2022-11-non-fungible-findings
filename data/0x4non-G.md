# gas

## Upgrade OZ libs to latest version

Upgrade OZ to latest version that has tons of gas optimizations 

```diff
diff --git a/package.json b/package.json
index a045a74..e64fc3b 100644
--- a/package.json
+++ b/package.json
@@ -58,8 +58,8 @@
     "@makerdao/hardhat-utils": "^0.1.3",
     "@nomiclabs/ethereumjs-vm": "^4.2.2",
     "@nomiclabs/hardhat-web3": "^2.0.0",
-    "@openzeppelin/contracts": "4.4.1",
-    "@openzeppelin/contracts-upgradeable": "^4.6.0",
+    "@openzeppelin/contracts": "4.8.0",
+    "@openzeppelin/contracts-upgradeable": "^4.8.0",
     "@types/chai-almost": "^1.0.1",
     "@types/yargs": "^17.0.7",
     "chai-almost": "^1.0.1",
```



## Some functions on `Pool`should be marked as `external`

These functions are not being used by the contract and can be marked as `external` to save gas.
- [`withdraw`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44)
- [`transferFrom`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58)
- [`balanceOf`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L79)
- [`totalSupply`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83)

## Optimization of `Pool:_transfer` method

Consider this optimization;
```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..6746263 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -68,19 +68,18 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
     }
 
     function _transfer(address from, address to, uint256 amount) private {
-        require(_balances[from] >= amount);
         require(to != address(0));
         _balances[from] -= amount;
-        _balances[to] += amount;
+        unchecked { _balances[to] += amount; }
 
         emit Transfer(from, to, amount);
     }
```

We can safely remove `require(_balances[from] >= amount);` because will revert with underflow on `_balances[from] -= amount;` if this condition cant be keeped.
Also we can safely add an `unchecked` wrap to `_balances[to] += amount;` also because of the previous check.
Please take a look to solmate erc20 implementation;
https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC20.sol#L76-L88

## Optimize `Exchange::incrementNonce` method

On [`Exchange::incrementNonce`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L315-L318)

Instead of
```
    function incrementNonce() external {
        nonces[msg.sender] += 1;
        emit NonceIncremented(msg.sender, nonces[msg.sender]);
    }
```
You could just;
```
    function incrementNonce() external {
      unchecked {
        emit NonceIncremented(msg.sender, nonces[msg.sender]++);
      }
    }
```
Its safe to use unchecked, its virtually impossible to overflow, also you could save the extra `SLOAD` inlining everything (or using an internal variable to save the new nonce)

## The modifier `internalCall` is used only once, just add the require to save gas
The modifier `internalCall` on [Exchange.sol#L48-L50](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L48-L50) is used only once in `_execute` [Exchange.sol#L238](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L238) so you could just add the inline require to save gas;

```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..6d438e8 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -235,8 +235,8 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
         public
         payable
         reentrancyGuard
-        internalCall
     {
+        require(isInternal, "This function should not be called directly");
         require(sell.order.side == Side.Sell);
 
         bytes32 sellHash = _hashOrder(sell.order, nonces[sell.order.trader]);
```

## Add unchecke on safe math operations
### On [Exchange.sol#L574](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L574) its safe to add unchecked due previous check `require(remainingETH >= price);`
```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..96b468f 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -571,7 +573,7 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
     ) internal {
         if (msg.sender == buyer && paymentToken == address(0)) {
             require(remainingETH >= price);
-            remainingETH -= price;
+            unchecked { remainingETH -= price; }
         }
 
         /* Take fee. */
```

### On [Pool.sol#L36::deposit](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L36) its safe to add unchecked
```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..f1a0dec 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -33,7 +33,7 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      * @dev deposit ETH into pool
      */
     function deposit() public payable {
-        _balances[msg.sender] += msg.value;
+        unchecked { _balances[msg.sender] += msg.value; }
         emit Transfer(msg.sender, address(0), msg.value);
     }
```

### On [Pool.sol#L44::withdraw]https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44 you could do unchecked or remove the require

Option 1:
```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..578f7e7 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -42,7 +42,6 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      * @param amount Amount to withdraw
      */
     function withdraw(uint256 amount) public {
-        require(_balances[msg.sender] >= amount);
         _balances[msg.sender] -= amount;
         (bool success,) = payable(msg.sender).call{value: amount}("");
         require(success);
```
The require is not necessary because it will trigger an underflow revert, but if you still want to use the require to add a clear revert message you could do an unchecked operation after;

```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..2f15b01 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -43,7 +43,7 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      */
     function withdraw(uint256 amount) public {
         require(_balances[msg.sender] >= amount);
-        _balances[msg.sender] -= amount;
+        unchecked { _balances[msg.sender] -= amount; }
         (bool success,) = payable(msg.sender).call{value: amount}("");
         require(success);
         emit Transfer(address(0), msg.sender, amount);
```

### Send ether on using a yul implementation on [Pool.sol#L47](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L47)

Since you are already doing that on `_returnDust` you could do it also here;
```diff
diff --git a/contracts/Pool.sol b/contracts/Pool.sol
index 3722082..2b2102a 100644
--- a/contracts/Pool.sol
+++ b/contracts/Pool.sol
@@ -43,9 +43,13 @@ contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {
      */
     function withdraw(uint256 amount) public {
         require(_balances[msg.sender] >= amount);
-        _balances[msg.sender] -= amount;
-        (bool success,) = payable(msg.sender).call{value: amount}("");
-        require(success);
+        unchecked { _balances[msg.sender] -= amount; }
+        bool success;
+        /// @solidity memory-safe-assembly
+        assembly {
+            // Transfer the ETH and store if it succeeded or not.
+            success := call(gas(), caller(), amount, 0, 0, 0, 0)
+        }
         emit Transfer(address(0), msg.sender, amount);
     }

```


## Mark functions as payable (with discretion)
You can mark public or external functions as payable to save gas. Functions that are not payable have additional logic to check if there was a value sent with a call, however, making a function payable eliminates this check. This optimization should be carefully considered due to potentially unwanted behavior when a function does not need to accept ether.

My recommendation is to mark all `onlyOwner` functions as payable on lines;
- [Exchange.sol#L56](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L56)
- [Exchange.sol#L60](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L60)
- [Exchange.sol#L66](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L66)
- [Exchange.sol#L325](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L325)
- [Exchange.sol#L334](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L334)
- [Exchange.sol#L343](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L343)
- [Exchange.sol#L352](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L352)


