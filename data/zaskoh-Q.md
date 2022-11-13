# Upgrade open zeppelin contract dependency
An outdated OZ version is used (which has known vulnerabilities, see https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

Currently used:
```bash
"@openzeppelin/contracts": "4.4.1",
"@openzeppelin/contracts-upgradeable": "^4.6.0",
```
Consider updating to > 4.7 / 4.8

# Use specific imports instead of just a global import
It's better to use the specific imports instead of just a global import. This helps to have a better overview what is realy needed and helps the developer to have a clearer view of the contract.

contracts/Pool.sol (resolved)
```bash
4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
5: import {OwnableUpgradeable} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

7: import {IPool} from "./interfaces/IPool.sol";
```

contracts/Exchange.sol (resolved)
```bash 
05: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol"; 
06: import {OwnableUpgradeable} from "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol"; 

08: import {ReentrancyGuarded} from "./lib/ReentrancyGuarded.sol"; 
09: import {EIP712} from "./lib/EIP712.sol"; 
10: import {MerkleVerifier} from "./lib/MerkleVerifier.sol"; 
11: import {IExchange} from "./interfaces/IExchange.sol"; 
12: import {IPool} from "./interfaces/IPool.sol"; 
13: import {IExecutionDelegate} from "./interfaces/IExecutionDelegate.sol"; 
14: import {IPolicyManager} from "./interfaces/IPolicyManager.sol"; 
15: import {IMatchingPolicy} from "./interfaces/IMatchingPolicy.sol";
```

# Unnecessarily import
The contracts/Exchange.sol contract has a unnecessary import that is never used and decrease readability.

Unused import:
```bash
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

# Consider two-phase ownsership transfer
Owner can call Pool.transferOwnership / Exchange.transferOwnership function to transfers the ownership to the new address directly. As such, there is a risk that the ownership is transferred to an invalid address, thus causing the contract to be without a owner.

# add indexed for hash in event to better query for cancelled hashes
As lot's of automated systems will be dependent on this information, it is best to add an indexed for this data.

```bash
File: contracts/Exchange.sol
99:     event OrderCancelled(bytes32 hash);
```

# dont index structs as its only a keccak256-hash
An indexed on a struct etc. is just the keccak256.  
You can add the attribute indexed to up to three parameters which adds them to a special data structure known as “topics” instead of the data part of the log. **A topic can only hold a single word (32 bytes)** so if you use a reference type for an indexed argument, the Keccak-256 hash of the value is stored as a topic instead.
```bash
File: contracts/Exchange.sol
102:     event NewExecutionDelegate(IExecutionDelegate indexed executionDelegate);
103:     event NewPolicyManager(IPolicyManager indexed policyManager);
```

# revert with a custom Error (also mentioned in gas-findings)
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

# Use if and throw custom error instead of empty require()
Here you can use a custom error to also give an error message back to the user.
```bash
File: contracts/Pool.sol
44:     function withdraw(uint256 amount) public { 
45:         require(_balances[msg.sender] >= amount);

```

# Update / Add NatSpec comments
NatSpec is missing or incomplete at some places.

Missing just @notice
```bash
File: contracts/Pool.sol
09: /** 
10:  * @title Pool
11:  * @dev ETH pool; funds can only be transferred by Exchange or Swap
12:  */
13: contract Pool is IPool, OwnableUpgradeable, UUPSUpgradeable {

File: contracts/Pool.sol
25:     /**
26:      * @dev receive deposit function
27:      */
28:     receive() external payable {

File: contracts/Pool.sol
32:     /**
33:      * @dev deposit ETH into pool
34:      */ 
35:     function deposit() public payable {

File: contracts/Pool.sol
40:     /**
41:      * @dev withdraw ETH from pool
42:      * @param amount Amount to withdraw
43:      */ 
44:     function withdraw(uint256 amount) public {

File: contracts/Exchange.sol
149:     /**
150:      * @dev _execute wrapper 
151:      * @param sell Sell input
152:      * @param buy Buy input
153:      */
154:     function execute(Input calldata sell, Input calldata buy)

File: contracts/Exchange.sol
164:     /**
165:      * @dev Bulk execute multiple matches
166:      * @param executions Potential buy/sell matches
167:      */
168:     function bulkExecute(Execution[] calldata executions)

File: contracts/Exchange.sol
229:     /**
230:      * @dev Match two orders, ensuring validity of the match, and execute all associated state transitions. Protected against reentrancy by a contract-global lock. Must be called internally.
231:      * @param sell Sell input
232:      * @param buy Buy input
233:      */
234:     function _execute(Input calldata sell, Input calldata buy)

File: contracts/Exchange.sol
285:     /**
286:      * @dev Cancel an order, preventing it from being matched. Must be called by the trader of the order
287:      * @param order Order to cancel
288:      */
289:     function cancelOrder(Order calldata order) public {

File: contracts/Exchange.sol
302:     /**
303:      * @dev Cancel multiple orders
304:      * @param orders Orders to cancel
305:      */
306:     function cancelOrders(Order[] calldata orders) external {

File: contracts/Exchange.sol
312:     /**
313:      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
314:      */
315:     function incrementNonce() external { 

```

Missing @notice and @return
```bash
File: contracts/Pool.sol
52:     /**
53:      * @dev transferFrom Transfer balances within pool; only callable by Swap and Exchange
54:      * @param from Pool fund sender
55:      * @param to Pool fund recipient
56:      * @param amount Amount to transfer
57:      */
58:     function transferFrom(address from, address to, uint256 amount)
```

Missing complete NatSpec
```bash
File: contracts/Pool.sol
79:     function balanceOf(address user) public view returns (uint256) {

File: contracts/Pool.sol
83:     function totalSupply() public view returns (uint256) {

File: contracts/Exchange.sol
35:     modifier whenOpen() { 

File: contracts/Exchange.sol
40:     modifier setupExecution() {

File: contracts/Exchange.sol
48:     modifier internalCall() {

File: contracts/Exchange.sol
56:     function open() external onlyOwner {

File: contracts/Exchange.sol
60:     function close() external onlyOwner { 

File: contracts/Exchange.sol
90:     event OrdersMatched(

File: contracts/Exchange.sol
099:     event OrderCancelled(bytes32 hash);
100:     event NonceIncremented(address indexed trader, uint256 newNonce);
102:     event NewExecutionDelegate(IExecutionDelegate indexed executionDelegate);
103:     event NewPolicyManager(IPolicyManager indexed policyManager);
104:     event NewOracle(address indexed oracle);
105:     event NewBlockRange(uint256 blockRange);

File: contracts/Exchange.sol
112:     function initialize(

File: contracts/Exchange.sol
323:     function setExecutionDelegate(IExecutionDelegate _executionDelegate)

File: contracts/Exchange.sol
332:     function setPolicyManager(IPolicyManager _policyManager)

File: contracts/Exchange.sol
341:     function setOracle(address _oracle) 

File: contracts/Exchange.sol
350:     function setBlockRange(uint256 _blockRange)
```
