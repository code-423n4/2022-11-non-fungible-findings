# 1. [G-1] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File contracts/Exchange.sol, line 184:     for (uint8 i = 0; i < executionsLength; i++)
    File contracts/Exchange.sol, line 307:     for (uint8 i = 0; i < orders.length; i++)
    File contracts/Exchange.sol, line 598:     for (uint8 i = 0; i < fees.length; i++)

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 2. [G-2] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File contracts/Pool.sol, line 36:     _balances[msg.sender] += msg.value;

    File contracts/Pool.sol, line 45-46:     
                    require(_balances[msg.sender] >= amount);
                    _balances[msg.sender] -= amount;

    File contracts/Pool.sol, line 73-74:     
                    _balances[from] -= amount;
                    _balances[to] += amount;

    File contracts/Exchange.sol, line 574:     remainingETH -= price;
    File contracts/Exchange.sol, line 599:     uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
    File contracts/Exchange.sol, line 601:     totalFee += fee;
    File contracts/Exchange.sol, line 607:     uint256 receiveAmount = price - totalFee;

# 3. [G-3] Storage: Emitting storage values

The values emitted shouldn't be read from storage. The existing memory values should be used instead in here:

    File contracts/Exchange.sol, line 328 - 329:     
                    executionDelegate = _executionDelegate;
                    emit NewExecutionDelegate(executionDelegate);          // I suggest:  emit NewExecutionDelegate(_executionDelegate);

    File contracts/Exchange.sol, line 337- 338:     
                    policyManager = _policyManager;
                    emit NewPolicyManager(policyManager);          // I suggest:  emit NewPolicyManager(_policyManager);

    File contracts/Exchange.sol, line 346- 347:     
                    oracle = _oracle;
                    emit NewOracle(oracle);          // I suggest:  emit NewOracle(_oracle);

    File contracts/Exchange.sol, line 354- 355:     
                    blockRange = _blockRange;
                    emit NewBlockRange(blockRange);          // I suggest:  emit NewBlockRange(_blockRange);

# 4. [G-4] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

There are 6 instances of this issue:

    File contracts/Pool.sol, line 36:     _balances[msg.sender] += msg.value;

    File contracts/Pool.sol, line 45-46:     
                    _balances[msg.sender] -= amount;

    File contracts/Pool.sol, line 73-74:     
                    _balances[from] -= amount;
                    _balances[to] += amount;

    File contracts/Exchange.sol, line 574:     remainingETH -= price;
    File contracts/Exchange.sol, line 601:     totalFee += fee;