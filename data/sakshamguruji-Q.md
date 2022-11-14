## EVENT IS MISSING INDEXED FIELDS

### Description:

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index
field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields).
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for
the events in question. If there are fewer than three fields, all of the fields should be indexed.

Affected Instances:
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L23
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L90
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L99
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L100
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L105

## REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

### Description:

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

Affected Instances:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L49
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L295
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L604

## MISSING 0 ADDRESS CHECK IN INITIALISER FUNCTION

### Description:

In the initialize function there is no 0 address check on the value of `_oracle` address , if accidentally set to 0 address it may force the protocol to be 
redeployed i.e. a faulty deployment. 
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L115
Introduce a 0 check here.

## LINE TOO LONG

###  Description:

Comments line too long may reduce the readability of the code . The comments should be structured appropriately.

Affected Code:

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L230