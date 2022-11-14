# [01] Lack of check if dust ether transfer is successful

The assembly low level call does not check if the transfer is successful. This can result in silent failures.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212-L227

Consider checking the returned value. E.g. `if iszero(callStatus) { revert(0, 0) }`.

# [02] Missing storage gap for upgradeable contracts

Consider adding a `__gap[]` storage variable to allow for new storage variables in later versions. [Example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol).

See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable issue. Adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L30

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L13

# [03] Missing address(0) check on initialize

If the oracle variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L115

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L130

# [04] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

Consider adding a two-steps pattern on critical changes to avoid mistakenly transferring ownership of roles or critial functionalities to the wrong address.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L323

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L332

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L341

# [05] Variable naming convention

Following instance is camel case

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L186

Following instance is snake case

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L188

Consider using only one convention throughout the codebase.

# [06] Remove commented code

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L282

# [07] Open TODOs

Todos should be resolved to improve code quality maturity.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L18

# [08] Returning manual values and using the return named variables feature in the same function

Consider using only one approach. E.g. only returning manual valuse or only using the return named variables feature

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L537-L555

# [09] Missing NATSPEC

Consider adding natspec on all function to improve documentation.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212

# [10] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows private above public.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L212

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L234

# [11] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L546

# [12] Don't allow `_oracle` to be the same as the existing `oracle`

Consider checking if the new oracle is not the same as the existing oracle, e.g. `require(_oracle != oracle, "")`. This will prevent triggering unnecessary events.

# [13] Avoid using the optimizer if possible, due to it's potential security bugs which can affect the contracts in scope

Optimizations are being actively developed and are not considered safe. Check the following [audit](https://github.com/trailofbits/publications/blob/master/reviews/SeaportProtocol.pdf) recommending agaist deployments with the optimizer enabled (item 3 in the audit document).

If possible, consider measuring and documenting the tradeoffs when enabling the optimizer.

# [14] Public function not called by the contract should be declared external

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L79

# [15] Interchangeable usage of uint256 and uint

Following instance is using uint

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L116

Following instance is using uint256

https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L350

Consider using only on approach for declaring 256 bits unsigned interges.
