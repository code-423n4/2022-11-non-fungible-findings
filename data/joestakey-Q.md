# QA Report

A few logic issues were found. They are mainly centered around the upgradeability of the contracts, and are worth taking a look at to ensure the contracts are future-proof.


# L01 - Hardcoded `EXCHANGE` should be avoided

The `Pool` contract uses a [hardcoded constant](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L17) for the `Exchange` address.
As the `Exchange` is an upgradeable contract, this means `EXCHANGE` can point to an incorrect address if `Exchange` is upgraded.

In such cases, the `Pool` functionality will be broken as `transferFrom` will revert when called by the new `Exchange` because of [this check](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L62): users will not be able to use the `Pool` for their orders. 

## Impact

Low

## Tools Used

Manual Analysis


## Mitigation

- `Pool.EXCHANGE` should not be a constant
- Use an `initialize()` function in Pool to set the `EXCHANGE` address.
- Add an admin setter to allow the Pool owner to set a new `EXCHANGE` if it is upgraded.

# L02 - An order cannot be filled if `fees` is too large

`_transferFees` will [revert](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L598) if `fees` is too large, meaning the order will never be able to be filled, though valid.


## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Consider adding an upper limit on `order.fees`, which can be validated in `_validateOrderParameters`.

# L03 - Potential loss of `msg.value` in `_transferTo()`.

`_transferTo()` is used during an `execute` call to transfer `amount` from a buyer to a seller.
If the paymentToken is `POOL` or `WETH`, the call does not check `msg.value == 0`.

This means if a buyer mistakenly passes `ETH` during the call, that amount will be stuck in `Exchange`, as there is no sweeping function.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Consider adding a `msg.value == 0` check in the code blocks handling `POOL` and `WETH`. 

# L04 - Parent contracts of `Exchange` should be upgradeable

`Exchange` is an upgradeable contract and inherits `OwnableUpgradeable.sol`,`UUPSUpgradeable.sol`, `ReentrancyGuarded` and `EIP712`.

The first two are upgradeable but the last two are not.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Use upgradeable versions of `ReentrancyGuarded` and `EIP712`.

# L05 - `Exchange` is not properly initialized

`Exchange` follows the `UUPSUpgradeable` pattern, inheriting it from OpenZeppelin's library. But `Exchange.initialize` [does not](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L112) call `__UUPSUpgradeable_init`, meaning it is actually left uninitialized.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Add `__UUPSUpgradeable_init()` after `__Ownable_init()` to `initialize()`.

# L06 - Lack of zero address checks in `initialize`

Improper initialization by passing zero addresses to key variables would force redeployment.
Note that there is already a zero address check in every admin setter.

- [executionDelegate = _executionDelegate;](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L128)
- [policyManager = _policyManager;](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L129)
- [oracle = _oracle;](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L130)
- [blockRange = _blockRange;](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L131)

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Add zero address checks in `initialize()`.

# L07 - `Exchange` is an upgradeable contract and is missing a __gap[50] storage variable 

A [storage gap](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) allows for new storage variables in later versions.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Define a storage gap at the end of the storage variable definitions

# L08 - `listingTime` can be set by `taker` to use their matching policy

The `listingTime` of orders are compared [here](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L543), determining which policy will be used.

A `buyer` (or `seller`) can create an order to match an existing one and set a lower `listingTime` to become the `maker`.

This means the true order maker will become the `taker` in the execution of the orders.

This will not grieve them as they have to agree to the policy used anyway, but the behavior is unexpected.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Consider requiring the hash of the maker order in the taker signature.