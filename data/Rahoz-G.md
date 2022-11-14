## USING EXTERNAL RATHER THAN PUBLIC FOR FUNCTIONS, SAVES GAS
Some functions only call by external should declare at external visibility.
### Proof of Concept
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L58
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44

## Use ++ instead of += 1
In function `incrementNonce` we should use ++ to save gas
### Proof of Concept
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L316
### Recommended Mitigation Steps
```solidity
function incrementNonce() external {
    ++nonces[msg.sender];
    emit NonceIncremented(msg.sender, nonces[msg.sender]);
}
```

## Should switch between non-zero and non-zero instead of zero and non-zero
Variable `Exchange.isOpen` now switch between 0-1
We should consider to change it to 1-2 because convert from non-zero to non-zero is cheaper than non-zero to zero
According to EIP-1087: https://eips.ethereum.org/EIPS/eip-1087, it take 20,000 gas to set a slot from 0 to non-zero with SSTORE while it takes only 5000 gas for any other change
### Proof of Concept
https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L56-L63
### Recommended Mitigation Steps
```solidity
uint256 public isOpen = 1;
modifier whenOpen() {
    require(isOpen == 2, "Closed");
    _;
}
function open() external onlyOwner {
    isOpen = 2;
    emit Opened();
}
function close() external onlyOwner {
    isOpen = 1;
    emit Closed();
}
```

