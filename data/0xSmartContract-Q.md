## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Potential DOS in Contract Inheriting UUPSUpgradeable.sol | 1 |
|[L-02]| initialize() function can be called by anybody | 1 |
|[L-03]| The `whenOpen` modifier just pauses the `execute`  and `bulkExecute` function | 1 |
|[L-04]|`_returnDust`  function create dirty bits | 1 |
|[L-05]|Critical Address Changes Should Use Two-step Procedure | 3 |
|[L-06]| Owner can renounce Ownership  | 8 |
|[L-07]|Loss of precision due to rounding | 1 |
|[L-08]| Require messages are too short and unclear | 7 |
|[L-09]|Fee recipient may be address(0) | 1 |
|[L-10]| Exchange.sol  `_execute` buy.order.side not validated | 1 |

Total 10 issues
### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Not using the latest version of OpenZeppelin from dependencies |1|
| [N-02] |No same value input control  |1|
| [N-03] | `0 address` check | 1 |
| [N-04] | Omissions in Events | 4 |
| [N-05] | Add parameter to Event-Emit | 2|
| [N-06] |Include ``return parameters`` in _NatSpec comments_  | All Contracts |
| [N-07] |Solidity compiler optimizations can be problematic | 1 |
| [N-08] |NatSpec is missing | 10 |
| [N-09] |Signature Malleability of EVM's ecrecover() | 1 |
| [N-10] | Lines are too long  | 2 |
| [N-11] |Stop using v != 27 && v != 28 or v == 27 || v == 28 | 1 |
| [N-12] |`Empty blocks` should be _removed_ or _Emit_ something | 2 |
| [N-13] |Signature scheme does not support smart contracts  | 1 |

Total 13 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |

Total 1 suggestion


### [L-01] Potential DOS in Contract Inheriting UUPSUpgradeable.sol

https://github.com/code-423n4/2022-11-non-fungible/blob/main/scripts/deploy.ts#L18


The scripts/ folder outlines a number of deployment scripts used by the team. Some of the contracts deployed utilise the ERC1967 upgradeable proxy standard. 

This standard involves first deploying an implementation contract and later a proxy contract which uses the implementation contract as its logic. When users make calls to the proxy contract, the proxy contract will delegate call to the underlying implementation contract. 

`Exchange.sol` implement an `initialize()` function which aims to replace the role of the `constructor()` when deploying proxy contracts

`Exchange.sol` inherits `UUPSUpgradeable.sol`. It is important that the contract is deployed and initialized in the same transaction to avoid any malicious frontrunning. `Exchange.sol` may potentially have `initialized` variable be false, and if this happens, the malicious attacker would take over the control.

it would be worthwhile to ensure the proxy contract is deployed and initialized in the same transaction, or ensure the `initialize()` function is callable only by the deployer of the proxy contract. This could be set in the proxy contracts `constructor()`


## Recommended Mitigation Steps

As a result, a malicious attacker could monitor the Ethereum blockchain for bytecode that matches the  contract and frontrun the initialize() transaction to gain ownership of the contract. This can be repeated as a Denial Of Service (DOS) type of attack, effectively preventing  contract deployment, leading to unrecoverable gas expenses.

Add a control that makes `initialize()` only call the Deployer Contract;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```


In another solution; Using the LibRLP library, which makes it possible to pre-calculate Foundry's contract deploy addresses, and deploy simultaneously using the Atomic transaction feature of Foundry and EVM.

https://twitter.com/transmissions11/status/1518507047943245824?s=20&t=-SgkBERLJqFRHn7392GrbA

```js
pragma solidity >=0.8.0;
import "forge-std/Script.sol";
import {LibRLP} from "../../test/utils/LibRLP.sol";

abstract contract DeployBase is Script {

    function run() external {
        vm.startBroadcast();

        // Precomputed contract addresses, based on contract deploy nonces.
        // tx.origin is the address who will actually broadcast the contract creations below.
        address BlurExchangeAddewss = LibRLP.computeAddress(tx.origin, vm.getNonce(tx.origin) + 1);
        address proxyaddress = LibRLP.computeAddress(tx.origin, vm.getNonce(tx.origin) );
```
### [L-02]  initialize() function can be called by anybody

`initialize()` function can be called anybody when the contract is not initialized.

More importantly, if someone else runs this function, they will have full authority because of the `__Ownable_init()` function.
Also, there is no 0 address check in the address arguments of the initialize() function, which must be defined.

Here is a definition of `initialize()` function.

```solidity

contracts/Exchange.sol:
  111      /* Constructor (for ERC1967) */
  112:     function initialize(
  113:         IExecutionDelegate _executionDelegate,
  114:         IPolicyManager _policyManager,
  115:         address _oracle,
  116:         uint _blockRange
  117:     ) external initializer {
  118:         __Ownable_init();
  119:         isOpen = 1;
  120: 
  121:         DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
  122:             name              : NAME,
  123:             version           : VERSION,
  124:             chainId           : block.chainid,
  125:             verifyingContract : address(this)
  126:         }));
  127: 
  128:         executionDelegate = _executionDelegate;
  129:         policyManager = _policyManager;
  130:         oracle = _oracle;
  131:         blockRange = _blockRange;
  132:     }

```


### Recommended Mitigation Steps

Add a control that makes `initialize()` only call the Deployer Contract;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```
### [L-03] The `whenOpen` modifier just pauses the `execute`  and `bulkExecute` function


The `whenOpen` modifier prevents the function it is used from, and the modifier constructor starts with the value `1(true)` by default.
If `0(false)` is set by Owner, the function it is used with becomes inoperable


The purpose of the `whenOpen` modifier; Pausing the project or just blocking the use of certain functions?
This part is not understood

Because when this modifier is closed with close, the `execute` and `bulkExecute`  functions will not work, but all other functions will work, which will confuse users.

Apart from the confusion, it can also cause technical question marks.


### Proof of Concept

1- Alice makes transactions from the platform, transfers with execute, changes transaction nonces with `incrementNonce`

2- Pause the project with `whenOpen` by `owner`

3- But Owner can change `oracle address`  or `setBlockRang` 

4- With `Owner` whenOpen, it starts the project again at any time (ERC721 may be a suitable time for price manipulation)

5- All of these situations can lead to a question mark for users



### Recommendation;

1 - The purpose of the `whenOpen` modifier; Pausing the project or just blocking the use of certain functions? This part should be clarified and added to the documents.

2- When the project is paused, it can be ensured that it is included in critical features such as address change (There is no need to pause for these changes anyway). These clear user question marks

### [L-04]  `_returnDust`  function create dirty bits

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.
Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.


```solidity

 function _returnDust() private {
        uint256 _remainingETH = remainingETH;
        assembly {
            if gt(_remainingETH, 0) {
                let callStatus := call(
                    gas(),
                    caller(),
                    selfbalance(),
                    0,
                    0,
                    0,
                    0
                )
            }
        }
    }

```

Recommendation:
Add this comment to `_returnDust`  function;
/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.


### [L-05] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.
See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure


```solidity

3 results - 1 files

contracts/Exchange.sol:
  322  
  323:     function setExecutionDelegate(IExecutionDelegate _executionDelegate)
  324          external

  331  
  332:     function setPolicyManager(IPolicyManager _policyManager)
  333          external

  340  
  341:     function setOracle(address _oracle)
  342          external
```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [L-06] Owner can renounce Ownership

[Exchange.sol#L6](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L6)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The non-fungible Ownable used in this project contract implements `renounceOwnership` . This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.


`onlyOwner` functions;
```js


contracts/Exchange.sol:
   56:     function open() external onlyOwner {

   60:     function close() external onlyOwner {

   66:     function _authorizeUpgrade(address) internal override onlyOwner {}

  324      function setExecutionDelegate(IExecutionDelegate _executionDelegate) onlyOwner

  334:     function setPolicyManager(IPolicyManager _policyManager) onlyOwner

  342      function setOracle(address _oracle) onlyOwner

  352:     function setBlockRange(uint256 _blockRange)

contracts/Pool.sol:
  15:     function _authorizeUpgrade(address) internal override onlyOwner {}


```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-07] Loss of precision due to rounding


```solidity
contracts/Exchange.sol:

  599: uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;

```

### [L-08] Require messages are too short and unclear

**Context:**
```solidity
7 results - 2 files

contracts/Exchange.sol:
  

  240:         require(sell.order.side == Side.Sell);
   
  291:         require(msg.sender == order.trader);
 
  573:         require(remainingETH >= price);
  

contracts/Pool.sol:


  45:         require(_balances[msg.sender] >= amount);
  
  48:         require(success);
  
  71:         require(_balances[from] >= amount);
  72:         require(to != address(0));
```


**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.

### [L-09] Fee recipient may be address(0)

**Context:**
```solidity

contracts/Exchange.sol:
  600:             _transferTo(paymentToken, from, fees[i].recipient, fee);
```

**Description:**
The recipient of a fee may be address(0), leading to lost ETH.


### [L-10] Exchange.sol  `_execute` buy.order.side not validated

In `Exchhange.execute` , it is not validated that `buy.order.side == Side.Buy` (only the sell side is validated). With the current system, all policies ensure that, but it would also make sense to validate it in execute IMO. Future policies may not validate that and such a basic check should also not be the responsibility of a policy in my opinion.


### [NC-01] Not using the latest version of OpenZeppelin from dependencies

The package.json configuration file says that the project is using 4.6.0 of OpenZeppelin which has a not last update version


```js

package.json:
  61:     "@openzeppelin/contracts": "4.4.1",
  62:     "@openzeppelin/contracts-upgradeable": "^4.6.0",
```

**Recommendation:**
Use patched versions

### [NC-02] No same value input control


```solidity

contracts/Exchange.sol:
  340  
  341:     function setOracle(address _oracle)
  342:         external
  343:         onlyOwner
  344:     {
  345:         require(_oracle != address(0), "Address cannot be zero");
  346:         oracle = _oracle;
  347:         emit NewOracle(oracle);
  348:     }

```

**Recommendation:**
Add code like this;
`if (oracle == _oracle revert ADDRESS_SAME();`


### [NC-03] `0 address` check

0 address control should be done in these parts;

[Exchange.sol#L130](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L130)

**Recommendation:**
Add code like this;
`if (oracle == address(0)) revert ADDRESS_ZERO();`

### [NC-04] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;


https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L329
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L338
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L347
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L355


### [NC-05] Add parameter to Event-Emit

Some event-emit description hasn’t parameter. Add to parameter  for front-end website or client app , they can has that something has happened on the blockchain.

Events with no old value;

```solidity

2 results - 1 files


contracts/Exchange.sol:
   58:         emit Opened();

   62:         emit Closed();
```

### [NC-06] Include ``return parameters`` in _NatSpec comments_

**Context:**
All Contracts


https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis.

**Recommendation:**
Include return parameters in NatSpec comments

Recommendation Code Style:
 ```js
    /// @notice information about what a function does
    /// @param pageId The id of the page to get the URI for.
    /// @return Returns a page's URI if it has been minted 
    function tokenURI(uint256 pageId) public view virtual override returns (string memory) {
        if (pageId == 0 || pageId > currentId) revert("NOT_MINTED");

        return string.concat(BASE_URI, pageId.toString());
    }
```

### [NC-07] Solidity compiler optimizations can be problematic


```js
hardhat.config.ts:
  69    },
  70:   solidity: {
  71:     compilers: [
  72:       {
  73:         version: '0.8.17',
  74:         settings: {
  75:           metadata: {
  76:             bytecodeHash: 'none',
  77:           },
  78:           optimizer: {
  79:             enabled: true,
  80:             runs: 800,
  81:           },

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.



**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


### [NC-08] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

```solidity
10  results

contracts/Pool.sol:
  70:     function _transfer(address from, address to, uint256 amount) private {

  79:     function balanceOf(address user) public view returns (uint256) {

  83:     function totalSupply() public view returns (uint256) {

contracts/Exchange.sol:
  35:     modifier whenOpen() {

  40:     modifier setupExecution() {
  47: 
  48:     modifier internalCall() {
  52: 
  53:     event Opened();
  54:     event Closed();
  55: 
  56:     function open() external onlyOwner {
 
  60:     function close() external onlyOwner {

```

### [NC-09] Signature Malleability of EVM's ecrecover()

```solidity

contracts/Exchange.sol:
  523          require(v == 27 || v == 28, "Invalid v parameter");
  524:         address recoveredSigner = ecrecover(digest, v, r, s);
  525          if (recoveredSigner == address(0)) {

```

**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [NC-10] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that lengthReference:
https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length


```solidity

contracts/Exchange.sol:
  546:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);

  550:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);

```


### [NC-11] Stop using v != 27 && v != 28 or v == 27 || v == 28

```solidity
contracts/Exchange.sol:

  523:         require(v == 27 || v == 28, "Invalid v parameter");
```

See this for reference ;
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg


### [NC-12] `Empty blocks` should be _removed_ or _Emit_ something


**Description:**
Code contains empty block

```js
2 results - 2 files

contracts/Exchange.sol:
66:     function _authorizeUpgrade(address) internal override onlyOwner {}

contracts/Pool.sol:
15:     function _authorizeUpgrade(address) internal override onlyOwner {}
```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


### [NC-13] Signature scheme does not support smart contracts

Non-Fungible does not support EIP 1271 and therefore no signatures that are validated by smart contracts. This limits the applicability for protocols that want to build on top of it and persons that use smart contract wallets. Consider implementing support for it
https://eips.ethereum.org/EIPS/eip-1271

### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
