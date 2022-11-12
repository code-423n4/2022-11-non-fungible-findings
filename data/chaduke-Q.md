

QA1: https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L112
1) A zero-address check should be performed for each input address; 2) A isContract() check should be done for each input address to make sure they are valid contracts (https://docs.openzeppelin.com/contracts/2.x/api/utils); and 3) 
a range check should be done for ``_blockrange``. For example, if it is set to zero inadvertently, then all oracle authorization will fail (https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L414), which is not desirable.

Human input error can cause a disaster as some users might front-run transactions and take advantage of the chaos. A deployment is also costing and reputation-damaging.

QA2: https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50
Although this function debits the balance before sending ETH to the msg.sender to prevent an reentrancy attack, it is still advised to use a Reengrancyguard (https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44-L50) to protect it from a more sophisticated reentrancy attack. 

QA3:        https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L49
change it to
```
 emit Transfer(address(this), msg.sender, amount);
```
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L37
change it to
```
 emit Transfer(msg.sender, address(this), msg.value);
```

QA4: https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240
Also need to add the following`
```
require(buy.order.side == Side.Buy);
```

QA5: I think one of the reasons that the contracts defines ``_execute()`` as public but then restricts it as an internal-only is to support the delegatecall, and in turn the reason to use delegatecall is to support the bypassing of failed executions.
I would suggest to use the "try-catch" mechanism instead to support of the bypassing of failed executions. This can improve the efficiency and readability of the code. 

