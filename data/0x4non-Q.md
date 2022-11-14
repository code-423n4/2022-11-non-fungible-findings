# QA

## Low

### If `blockRange` is set to 0 or a small value executions will always revert
The require on line [Exchange.sol#L414](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L414) will always fail if you set to 0 (or will probably always fail with a small value)

Please add a minimum value check on `setBlockRange` function in [Exchange.sol#L354](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L354)

### Unexpected ether in `Pool` can break invariants

Consider the next invariant;
`SUM(balances) == totalSupply`

So the sum of all users balances is equal to the totalSupply. But since totalSupply is the balance of the contract a malicius user could [force feeding](https://consensys.github.io/smart-contract-best-practices/attacks/force-feeding/) chainging contract balances and breaking this invariant.

Consider keep track of totalSupply in a variable instead of using `address(this).balance` as you do on [Pool.sol#L83-L85](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L83-L85)


### `bulkExecute` function on [Exchange.sol#L168](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L168) should have `nonReentrant` modifier

The `bulkExecute` cant follow the check effects itteration pattern it would be best to add a `nonReentrant` modifier to avoid a reentrancy issue causing unexpected outputs.

### `Exchange.sol#L204-L206` :: Mising event emmision to log/notify if execution is success or fail
So you arent checking the `result` of the `delegatecall` because you dont want to revert if the execution is not successful, but you should emit events to track whats going on.

Recommendation, add event emmision to track delegate call output on [Exchange.sol#L204-L206](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L204-L206)

You also may reconsider this pattern... if a delegatecall fails, why you wouldnt want to revert the whole transaction?


## Non critical


### Empty revert messages

On:
[`Exchange.sol#L291`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L291)
[`Exchange.sol#L573`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L573)
[`Pool.sol#L45`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L45)
[`Pool.sol#L48`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L48)
[`Pool.sol#L71`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L71)
[`Pool.sol#L72`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L72)

Consider adding clear revert messages.

### Use named imports

Use named imports on [Exchange.sol#L4-L15](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L4-L15) and [Pool.sol#L4-L7](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L4-L7) to make clear the contracts you use like you do on lines [Exchange.sol#L16-L24](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L16-L24)


### `Exchange.sol` :: Remove temporary testing functions

On [Exchange.sol#L134-L142](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L134-L142) the function `updateDomainSeparator` is marked as `// temporary function for testing`.
This function should be removed for the production version.


### `Exchange.sol` :: Commented return should be deleted
There is a commented retun on [Exchange.sol#L282](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L282) that should be removed;
```
        // return (price);
```

### `Exchange.sol` :: TODO commented
Resolve and remove the follow comment on [Pool.sol#L18](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L18)
```// TODO: set proper address before deployment```

### `Exchange.sol` :: Address(0) check should be made outside the if

Consider moving out the if statement the `address(0)` check on [`Exchange.sol#L630`](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L630) 

From;
```diff
diff --git a/contracts/Exchange.sol b/contracts/Exchange.sol
index ec27b1d..3ab30c1 100644
--- a/contracts/Exchange.sol
+++ b/contracts/Exchange.sol
@@ -625,9 +626,9 @@ contract Exchange is IExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, U
             return;
         }
 
+        require(to != address(0), "Transfer to zero address");
         if (paymentToken == address(0)) {
             /* Transfer funds in ETH. */
-            require(to != address(0), "Transfer to zero address");
             (bool success,) = payable(to).call{value: amount}("");
             require(success, "ETH transfer failed");
         } else if (paymentToken == POOL) {
```

