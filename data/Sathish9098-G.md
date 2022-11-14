1)          ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS


There are 3 instances of this issue:

  FILE:  2022-11-non-fungible/contracts/Exchange.sol

184:     for (uint8 i = 0; i < executionsLength; i++) {

307:      for (uint8 i = 0; i < orders.length; i++)

598:      for (uint8 i = 0; i < fees.length; i++) {


---------------------------------------------------------------------------------------------------------------------------------------------

2)               UINT256 CAN BE USED INSTEAD OF UINT8. Uint256 uses less gas than uint8 in loops and other situations.

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

There are 6 instances of this issue:

  FILE:  2022-11-non-fungible/contracts/Exchange.sol

184:        for (uint8 i = 0; i < executionsLength; i++) {

307:        for (uint8 i = 0; i < orders.length; i++)

441:        uint8 v, 

477:        uint8 v; bytes32 r; bytes32 s; 

517:       uint8 v,

598:      for (uint8 i = 0; i < fees.length; i++) {



--------------------------------------------------------------------------------------------------------------------------------------------------------------------

3)          <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES . 

SAME FOR   <X> -= <Y> COSTS MORE GAS THAN <X> = <X> - <Y>


There are 7 instances of this issue:

FILE:  2022-11-non-fungible/contracts/Exchange.sol

316:         nonces[msg.sender] += 1;

572:         remainingETH -= price; 

601:         totalFee += fee;

2022-11-non-fungible/contracts/Pool.sol

36:          _balances[msg.sender] += msg.value;

46:          _balances[msg.sender] -= amount;

73:        _balances[from] -= amount;

74:         _balances[to] += amount; 



--------------------------------------------------------------------------------------------------------------------------------------------------------

4)               FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE


      If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

FILE:  2022-11-non-fungible/contracts/Exchange.sol

There are 4 instances of this issue:

function setExecutionDelegate(IExecutionDelegate _executionDelegate) 
        onlyOwner
    {

function setPolicyManager(IPolicyManager _policyManager)  
        external
        onlyOwner
    {

function setOracle(address _oracle)   
        external
        onlyOwner
    {

function setBlockRange(uint256 _blockRange)  
        external
        onlyOwner
   

----------------------------------------------------------------------------------------------------------------------------------------------------------

5)           IN THE RETURN STATEMENT   DON'T WANT TO CHECK ALL CONDITIONS BECAUSE WE ARE USING """&&""" OPERATOR . IN THE RETURN STATEMENT CONDITION CHECK IF ANY ONE   condition  FALSE THEN ALWAYS RETURN false EVEN OTHER CONDITIONS ARE true . SO WE DON'T WANT TO  CHECK ALL CONDITIONS.   IT WILL SAVE MORE GAS FEE. 


There are 4 instances of this issue:

FILE:  2022-11-non-fungible/contracts/Exchange.sol

return (
            /* Order must have a trader. */
            (order.trader != address(0)) &&
            /* Order must not be cancelled or filled. */
            (!cancelledOrFilled[orderHash]) &&
            /* Order must be settleable. */
            (order.listingTime < block.timestamp) &&
            (block.timestamp < order.expirationTime)
        );


412:     if (order.order.extraParams.length > 0 && order.order.extraParams[0] == 0x01) {

572:      if (msg.sender == buyer && paymentToken == address(0))

Pool.sol

63:    if (msg.sender != EXCHANGE && msg.sender != SWAP)

---------------------------------------------------------------------------------------------------------------------------------------------------------------

6)             ADD UNCHECKED  FOR ARITHMATIC OPERATIONS WHERE THE OPERANDS CANNOT UNDERFLOW

FILE:  2022-11-non-fungible/contracts/Exchange.sol

There are 3 instances of this issue:

414:           require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

599:          uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;

607:          uint256 receiveAmount = price - totalFee;


---------------------------------------------------------------------------------------------------------------------------------------------------------------

7)            INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

FILE:  2022-11-non-fungible/contracts/Exchange.sol

function _validateUserAuthorization(
        bytes32 orderHash,
        address trader,
        uint8 v,
        bytes32 r,
        bytes32 s,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature
    ) internal view returns (bool) {


 function _canMatchOrders(Order calldata sell, Order calldata buy) 
        internal
        view
        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)

--------------------------------------------------------------------------------------------------------------------------------------------------------------

8)            when using || operators in condition check we don't want check all conditions . Any one condition true it always return true . For every condition checks skipped can reduce the gas fee . 

FILE:  2022-11-non-fungible/contracts/Exchange.sol

523:      require(v == 27 || v == 28, "Invalid v parameter");


--------------------------------------------------------------------------------------------------------------------------------------------------------------

9)            UNWANTED USE OF THE VARIABLE  receiveAmount. INSTEAD ASSIGN price - totalFee VALUE TO  uint256 receiveAmount CAN DIRECTLY 

PASS price - totalFee  IN THE RETURN STAEMENT . IN THIS WAY WE CAN AVOID THE GAS  

FILE:  2022-11-non-fungible/contracts/Exchange.sol

uint256 receiveAmount = price - totalFee; 
        return (receiveAmount);

----------------------------------------------------------------------------------------------------------------------------------------------------------------
 
10)          state variable POOL and WETH can be cached

 FILE:  2022-11-non-fungible/contracts/Exchange.sol

633:      } else if (paymentToken == POOL) {

635:     bool success = IPool(POOL).transferFrom(from, to, amount);

637:     } else if (paymentToken == WETH) {

639:     executionDelegate.transferERC20(WETH, from, to, amount);


-------------------------------------------------------------------------------------------------------------------------------------------------------
11)          Use the external Visibility Modifier

Use the external function visibility for gas optimization because the public visibility modifier is equivalent to using the external and internal visibility modifier, meaning both public and external can be called from outside of your contract, which requires more gas.

We are not calling any functions inside the contract or inside any functions . its its better we can use external instead of public.

FILE:  2022-11-non-fungible/contracts/Pool.sol


35:    function deposit() public payable {

44:    function withdraw(uint256 amount) public {

  function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
    {

function balanceOf(address user) public view returns (uint256) {
        return _balances[user];
    }
 function totalSupply() public view returns (uint256) {
        return address(this).balance;
    }

---------------------------------------------------------------------------------------------------------------------------------------------

