## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR | 4 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing Contract-existence Checks Before Low-level Calls | 2 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 10 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Upgrade OpenZeppelin Contract Dependency | 2 |

Total: 19 contexts over 5 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 4 |
| [NC&#x2011;2](#NC&#x2011;2) | Public Functions Not Called By The Contract Should Be Declared External Instead | 3 |
| [NC&#x2011;3](#NC&#x2011;3) | `require()` / `revert()` Statements Should Have Descriptive Reason Strings | 7 |
| [NC&#x2011;4](#NC&#x2011;4) | Non-usage of specific imports | 9 |
| [NC&#x2011;5](#NC&#x2011;5) | Open TODOs | 1 |

Total: 24 contexts over 5 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```
115: function initialize: address _oracle
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L115



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR

The protocol calculates the chainid it should assign during its execution and permanently stores it in an immutable variable. Should Ethereum fork in the feature, the chainid will change however the one used by the permits will not enabling a user to use any new permits on both chains thus breaking the token on the forked chain permanently.

Please consult EIP1344 for more details: https://eips.ethereum.org/EIPS/eip-1344#rationale

#### <ins>Proof Of Concept</ins>

```
121: DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : NAME,
            version           : VERSION,
            chainId           : block.chainid,
            verifyingContract : address(this)
        }));
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L121

```
136: DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : NAME,
            version           : VERSION,
            chainId           : block.chainid,
            verifyingContract : address(this)
        }));
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L136



#### <ins>Recommended Mitigation Steps</ins>

The mitigation action that should be applied is the calculation of the chainid dynamically on each permit invocation. As a gas optimization, the deployment pre-calculated hash for the permits can be stored to an immutable variable and a validation can occur on the permit function that ensure the current chainid is equal to the one of the cached hash and if not, to re-calculate it on the spot.



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```
631: (bool success,) = payable(to).call{value: amount}("");
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L631

```
47: (bool success,) = payable(msg.sender).call{value: amount}("");
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L47




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```
4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
6: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L4-L6

```
4: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
5: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L4-L5



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

#### <ins>Proof Of Concept</ins>


```
@openzeppelin/contracts: 4.4.1
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/package.json#L61

```
@openzeppelin/contracts-upgradeable: ^4.6.0
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/package.json#L62



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json



## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```
323: function setExecutionDelegate(IExecutionDelegate _executionDelegate)
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L323

```
332: function setPolicyManager(IPolicyManager _policyManager)
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L332

```
341: function setOracle(address _oracle)
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L341

```
350: function setBlockRange(uint256 _blockRange)
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L350



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.






### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```
function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L289

```
function withdraw(uint256 amount) public {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L44

```
function transferFrom(address from, address to, uint256 amount)
        public
        returns (bool)
    {
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L58





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> `require()` / `revert()` Statements Should Have Descriptive Reason Strings

#### <ins>Proof Of Concept</ins>


```
240: require(sell.order.side == Side.Sell);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L240

```
291: require(msg.sender == order.trader);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L291

```
573: require(remainingETH >= price);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L573

```
45: require(_balances[msg.sender] >= amount);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L45

```
48: require(success);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L48

```
71: require(_balances[from] >= amount);
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L71

```
72: require(to != address(0));
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L72





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### <ins>Proof Of Concept</ins>


```
8: import "./lib/ReentrancyGuarded.sol";
9: import "./lib/EIP712.sol";
10: import "./lib/MerkleVerifier.sol";
11: import "./interfaces/IExchange.sol";
12: import "./interfaces/IPool.sol";
13: import "./interfaces/IExecutionDelegate.sol";
14: import "./interfaces/IPolicyManager.sol";
15: import "./interfaces/IMatchingPolicy.sol";
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Exchange.sol#L8-L15

```
7: import "./interfaces/IPool.sol";
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L7




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.




### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Open TODOs

An open TODO is present. It is recommended to avoid open TODOs as they may indicate programming errors that still need to be fixed.

#### <ins>Proof Of Concept</ins>


```
18: // TODO: set proper address before deployment
```

https://github.com/code-423n4/2022-11-non-fungible/tree/main/contracts/Pool.sol#L18






