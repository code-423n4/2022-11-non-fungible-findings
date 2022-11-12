### Title
Long Revert Strings

#### Impact
Issue Information: Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-11-non-fungible/contracts/Exchange.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-11-non-fungible/contracts/Exchange.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
2022-11-non-fungible/contracts/Exchange.sol::6 => import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
2022-11-non-fungible/contracts/Exchange.sol::13 => import "./interfaces/IExecutionDelegate.sol";
2022-11-non-fungible/contracts/Exchange.sol::49 => require(isInternal, "This function should not be called directly");
2022-11-non-fungible/contracts/Exchange.sol::295 => require(!cancelledOrFilled[hash], "Order already cancelled or filled");
2022-11-non-fungible/contracts/Exchange.sol::604 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-11-non-fungible/contracts/Pool.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
2022-11-non-fungible/contracts/Pool.sol::5 => import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
2022-11-non-fungible/contracts/mocks/MockERC1155.sol::3 => import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
2022-11-non-fungible/contracts/mocks/MockERC20.sol::3 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
2022-11-non-fungible/contracts/mocks/MockERC721.sol::3 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-11-non-fungible/contracts/mocks/MockERC721.sol::4 => import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
2022-11-non-fungible/contracts/test/TestStandardPolicyERC1155.sol::5 => import {IMatchingPolicy} from "../interfaces/IMatchingPolicy.sol";
```
#### Tools used
Manual