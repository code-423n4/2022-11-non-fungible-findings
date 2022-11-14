1)++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops


File: 2022-11-non-fungible\contracts\Exchange.sol
  177,9:         for (uint8 i=0; i < executionsLength; i++) {
  184,9:         for (uint8 i = 0; i < executionsLength; i++) {
  307,9:         for (uint8 i = 0; i < orders.length; i++) {
  598,9:         for (uint8 i = 0; i < fees.length; i++) { 
  
2)X = X + Y OR X = X - Y CAN BE USED INSTEAD OF X += Y OR X -= Y

x = x + y or x = x - y costs less gas than x += y or x -= y. For example, v += 27 can be changed to v = v + 27 in the following code.   

File: 2022-11-non-fungible\contracts\Exchange.sol
  316,28:         nonces[msg.sender] += 1;
  574,26:             remainingETH -= price; 
  601,22:             totalFee += fee;

File: 2022-11-non-fungible\contracts\Pool.sol
  36,31:         _balances[msg.sender] += msg.value;
  46,31:         _balances[msg.sender] -= amount;
  73,25:         _balances[from] -= amount;
  74,23:         _balances[to] += amount;  
  
3)Multiple address mappings can be combined into a single mapping of an address to a struct

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

File: 2022-11-non-fungible\contracts\Exchange.sol
  85,5:     mapping(bytes32 => bool) public cancelledOrFilled;
  86,5:     mapping(address => uint256) public nonces; 

4)Use assembly to check for address(0)
Impact

File: 2022-11-non-fungible\contracts\Exchange.sol
  327,48:         require(address(_executionDelegate) != address(0), "Address cannot be zero");
  336,44:         require(address(_policyManager) != address(0), "Address cannot be zero");
  345,28:         require(_oracle != address(0), "Address cannot be zero");
  525,32:         if (recoveredSigner == address(0)) {
  572,52:         if (msg.sender == buyer && paymentToken == address(0)) {
  628,29:         if (paymentToken == address(0)) {
  630,27:             require(to != address(0), "Transfer to zero address");  
  
File: 2022-11-non-fungible\contracts\Pool.sol

  72,23:         require(to != address(0));  