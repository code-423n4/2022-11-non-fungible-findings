# add external payable as not a enduser function
For all the functions that are not for endusers and only handled by admins it makes sense to add payable to save gas.

```bash
File: contracts/Exchange.sol
56:     function open() external onlyOwner {

File: contracts/Exchange.sol // need to change also in interface
60:     function close() external onlyOwner {

File: contracts/Exchange.sol // need to change also in interface
323:     function setExecutionDelegate(IExecutionDelegate _executionDelegate)
324:         external
325:         onlyOwner
326:     {

File: contracts/Exchange.sol // need to change also in interface
332:     function setPolicyManager(IPolicyManager _policyManager)
333:         external 
334:         onlyOwner
335:     {

File: contracts/Exchange.sol // need to change also in interface
341:     function setOracle(address _oracle)
342:         external 
343:         onlyOwner
344:     {

File: contracts/Exchange.sol // need to change also in interface
350:     function setBlockRange(uint256 _blockRange)
351:         external
352:         onlyOwner
353:     {

// also change public to external as not called internally and change in interface
File: contracts/Pool.sol
58:     function transferFrom(address from, address to, uint256 amount)
59:         public // need to change also in interface
60:         returns (bool)
61:     {

```

# change function visibility to external if function is not used internally
If a function is not used internally from the contract you should change it to external to save some gas.
```shell
File: contracts/Pool.sol
79:     function balanceOf(address user) public view returns (uint256) {

File: contracts/Pool.sol
83:     function totalSupply() public view returns (uint256) {

File: contracts/Pool.sol
44:     function withdraw(uint256 amount) public {

```

# use unchecked{} if overflow is not possible
If you have checked variables before any calculation and it's clear that it's not possible to overflow, you should put in the unchecked to save gas.

```bash
File: contracts/Pool.sol
35:     function deposit() public payable {
36:         _balances[msg.sender] += msg.value; // you can use unchecked, to overflow it would mean a single address would need more than uint256.max of wei.

File: contracts/Pool.sol
44:     function withdraw(uint256 amount) public {
45:         require(_balances[msg.sender] >= amount);
46:         _balances[msg.sender] -= amount; // use unchecked, we know it cant overflow because of L45

File: contracts/Pool.sol
70:     function _transfer(address from, address to, uint256 amount) private {
71:         require(_balances[from] >= amount);
72:         require(to != address(0));
73:         _balances[from] -= amount; // use unchecked, we know it cant overflow because of L71

File: contracts/Exchange.sol
572:         if (msg.sender == buyer && paymentToken == address(0)) {
573:             require(remainingETH >= price);
574:             remainingETH -= price; // use unchecked, we know it cant underflow because of L573
575:         }

File: contracts/Exchange.sol
315:     function incrementNonce() external {         
316:         nonces[msg.sender] += 1; // user would need more than unit256.max transactions to the contract to overflow
317:         emit NonceIncremented(msg.sender, nonces[msg.sender]);
318:     }

```

# revert with a custom Error (also mentioned in qa)
Its better to use custom Errors instead of revert's with a long string. Change it to a custom error.

```bash
File: contracts/Pool.sol
62:         if (msg.sender != EXCHANGE && msg.sender != SWAP) {
63:             revert('Caller is not authorized to transfer');
64:         }

File: contracts/Exchange.sol
640:         } else {
641:             revert("Invalid payment token");
642:         }
```
For the revert in contracts/Exchange.sol you could also remove the else and just revert if the function comes to the end and add a return in every if.

# use variable instead of state var
if you access a state variable it costs 100gas, if you have a local variable that holds the value, just use it instead of the state variable.

```bash
File: contracts/Exchange.sol
323:     function setExecutionDelegate(IExecutionDelegate _executionDelegate)
324:         external
325:         onlyOwner
326:     {
327:         require(address(_executionDelegate) != address(0), "Address cannot be zero");
328:         executionDelegate = _executionDelegate;
329:         emit NewExecutionDelegate(executionDelegate); // use _executionDelegate instead
330:     }

File: contracts/Exchange.sol
332:     function setPolicyManager(IPolicyManager _policyManager)
333:         external 
334:         onlyOwner
335:     {
336:         require(address(_policyManager) != address(0), "Address cannot be zero");
337:         policyManager = _policyManager;
338:         emit NewPolicyManager(policyManager); // use _policyManager instead
339:     }

File: contracts/Exchange.sol
341:     function setOracle(address _oracle)
342:         external
343:         onlyOwner
344:     {
345:         require(_oracle != address(0), "Address cannot be zero");
346:         oracle = _oracle;
347:         emit NewOracle(oracle); // use _oracle instead
348:     }

File: contracts/Exchange.sol
350:     function setBlockRange(uint256 _blockRange)
351:         external
352:         onlyOwner
353:     {
354:         blockRange = _blockRange;
355:         emit NewBlockRange(blockRange); // use _blockRange instead
356:     }
```


# use 2 for isOpen in contracts/Exchange.sol to save gas
You can save gas if you change the behaviour to interprete  
isOpen = 1 => open  
isOpen = 2 => closed  
as you don't need to change the value from 0 to 1 again.

```shell
File: contracts/Exchange.sol
60:     function close() external onlyOwner { 
61:         isOpen = 0; // change to 2
62:         emit Closed();
63:     }
```

The modifier whenOpen() will also work while the contract is not initilized as he is checking for isOpen==1 and reverts if not.

# refactor _transferTo function to save a jump to the function
The function _transferTo( in contracts/Exchange.sol checks in the beginning if amount == 0 and just returns.
The function itself doesn't state that it has a return value and it's better to refactor to not call it if amount == 0.

```bash
File: contracts/Exchange.sol
621:     function _transferTo(
622:         address paymentToken,
623:         address from,
624:         address to,
625:         uint256 amount
626:     ) internal {
627:         //if (amount == 0) {
628:         //    return;
629:         //}

=> and in the calling functions:

File: contracts/Exchange.sol
580:         /* Transfer remainder to seller. */
581:         if (receiveAmount > 0) 
582:             _transferTo(paymentToken, buyer, seller, receiveAmount);

File: contracts/Exchange.sol
601:             if (fee > 0) {
602:                 _transferTo(paymentToken, from, fees[i].recipient, fee);
603:             }
```

# Summary if all gas optimization would be implemented
Here is the difference for REPORT_GAS=true hardhat test from a fresh clone to how it is with all changes implemented.

```git
@@ -9,27 +9,27 @@
·······················|························|·············|·············|·············|···············|··············
 |  ERC20              ·  transferFrom          ·      51832  ·      51844  ·      51838  ·           10  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  bulkExecute           ·     796257  ·     902099  ·     852656  ·            6  ·          -  │
+|  Exchange           ·  bulkExecute           ·     795929  ·     901689  ·     852301  ·            6  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  Exchange           ·  cancelOrder           ·      61198  ·      61365  ·      61324  ·            7  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  Exchange           ·  cancelOrders          ·          -  ·          -  ·      95362  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  close                 ·          -  ·          -  ·      29320  ·            3  ·          -  │
+|  Exchange           ·  close                 ·          -  ·          -  ·      34093  ·            3  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  execute               ·     252299  ·     307542  ·     273467  ·           24  ·          -  │
+|  Exchange           ·  execute               ·     252233  ·     307013  ·     273115  ·           24  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  incrementNonce        ·      32939  ·      50039  ·      41489  ·            8  ·          -  │
+|  Exchange           ·  incrementNonce        ·      32686  ·      49786  ·      41236  ·            8  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  open                  ·          -  ·          -  ·      51214  ·            3  ·          -  │
+|  Exchange           ·  open                  ·          -  ·          -  ·      34090  ·            3  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  setBlockRange         ·          -  ·          -  ·      31830  ·            1  ·          -  │
+|  Exchange           ·  setBlockRange         ·          -  ·          -  ·      31818  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  setExecutionDelegate  ·          -  ·          -  ·      35088  ·            1  ·          -  │
+|  Exchange           ·  setExecutionDelegate  ·          -  ·          -  ·      34979  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  setOracle             ·          -  ·          -  ·      32308  ·            1  ·          -  │
+|  Exchange           ·  setOracle             ·          -  ·          -  ·      32284  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Exchange           ·  setPolicyManager      ·          -  ·          -  ·      35129  ·            1  ·          -  │
+|  Exchange           ·  setPolicyManager      ·          -  ·          -  ·      35031  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  ExecutionDelegate  ·  approveContract       ·      47179  ·      47263  ·      47223  ·           21  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
@@ -49,9 +49,9 @@
 ······················|························|·············|·············|·············|···············|··············
 |  PolicyManager      ·  addPolicy             ·          -  ·          -  ·      92087  ·           10  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Pool               ·  deposit               ·      33095  ·      50195  ·      47345  ·           12  ·          -  │
+|  Pool               ·  deposit               ·      33021  ·      50121  ·      47271  ·           12  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  Pool               ·  withdraw              ·          -  ·          -  ·      40433  ·            2  ·          -  │
+|  Pool               ·  withdraw              ·          -  ·          -  ·      40346  ·            2  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  Deployments                                 ·                                         ·  % of limit   ·             │
 ···············································|·············|·············|·············|···············|··············
@@ -67,9 +67,9 @@
 ···············································|·············|·············|·············|···············|··············
 |  PolicyManager                               ·          -  ·          -  ·     580496  ·        1.9 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  Pool                                        ·          -  ·          -  ·     963075  ·        3.2 %  ·          -  │
+|  Pool                                        ·          -  ·          -  ·     913853  ·          3 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
 |  StandardPolicyERC721                        ·          -  ·          -  ·     219482  ·        0.7 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  TestExchange                                ·          -  ·          -  ·    3514198  ·       11.7 %  ·          -  │
+|  TestExchange                                ·          -  ·          -  ·    3481146  ·       11.6 %  ·          -  │
 ·----------------------------------------------|-------------|-------------|-------------|---------------|-------------·
```