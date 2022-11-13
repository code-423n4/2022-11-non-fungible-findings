Issue: Make sure you don't call an external function until you've done all the internal work you need to do. 

Code: https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L601-L602

Explanation: Given that _transferFees is an internal function there seems to be no "High risk" of a reentrancy attack. However totalFee is accumulated only after the end of _transferTo, which makes calls to other external contract.  A contract can pose as  recipient of fees and drain via a potential fallback function. Leasing to loss of funds.

(bool success,) = payable(to).call{value: amount}("");

Mitigation: To finish off all internal states calculation before extenal call. I.e. totalFee += fees before _transferTo (line 600)
