1)    ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

There are 2 instances of this issue:

  FILE:  2022-11-non-fungible/contracts/Exchange.sol

184:     for (uint8 i = 0; i < executionsLength; i++) {

307:      for (uint8 i = 0; i < orders.length; i++)


---------------------------------------------------------------------------------------------------------------------------------------------

2)               UINT256 CAN BE USED INSTEAD OF UINT8. Uint256 uses less gas than uint8 in loops and other situations.

                    A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

There are 2 instances of this issue:

  FILE:  2022-11-non-fungible/contracts/Exchange.sol

184:        for (uint8 i = 0; i < executionsLength; i++) {

307:      for (uint8 i = 0; i < orders.length; i++)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

3)          <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES


There are instances of this issue:

FILE:  2022-11-non-fungible/contracts/Exchange.sol

316:         nonces[msg.sender] += 1;

--------------------------------------------------------------------------------------------------------------------------------------------------------

4)    FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

      If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

FILE:  2022-11-non-fungible/contracts/Exchange.sol

There are instances of this issue:

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
   




