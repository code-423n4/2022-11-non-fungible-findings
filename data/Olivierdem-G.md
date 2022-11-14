Gas Report

    1.  Using uint smaller than 32 bytes incurs overhead. Each operation involving a uint8 costs an extra 22-28 gas (found line 184 in Exchange.sol, also found line 307 and line 598).
    2.  Do not use '-=' ('remainingETH -= price;', found line 574 in Exchange.sol). Instead use "remainingETH = remainingETH - price;" in order to save some gas.
    3.  Require() string longer than 32 bytes cost extra gas. For exemple, line 49 of Exchange.sol, the require error string ("This function should not be called directly") is 43 bytes long when "Can't call function directly" is 28 bytes long, and therefore cheaper in gas and just as clear.
    4.  Increment 'i' at the end of the loop in 'unchecked' so save gas. 'unchecked { ++i; }' at end of loop instead of 'for (uint8 i = 0; i < length; i++)'. (found line 184 of Exchange.sol).
    5. Use assembly to write storage value. Line 341 in setOracle(address _oracle) of Exchange.sol, instead of "oracle = _oracle;", use  assembly {sstore(oracle.slot, _oracle)} to save gas.
    6. Add 'unchecked{}' for subtractions where the operands cannot underflow because of previous if-statement. (found line 607 in _transferFees() of .Exchange.sol).
        require(totalFee <= price, "Total amount of fees are more than the price");
        uint256 receiveAmount = price - totalFee;)
    7. Do not use 0 and 1 for isOpen, use 1 and 2 like openzeppelin does for reentrancy check. Storage writes from a zero value (default value) to a non-zero value are expensive compared to non-zero to non-zero writes. See this: https://hackmd.io/@fvictorio/gas-costs-after-berlin.


