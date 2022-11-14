
# QA report

## Author: rotcivegaf

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Open TODO | 1 |
| [L&#x2011;02] | Remove testing function  | 1 |

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | `require` without custom errors | 7 |
| [N&#x2011;02] | Unused imports | 1 |
| [N&#x2011;03] | Lint | 7 |
| [N&#x2011;04] | Unused commented code lines | 4 |


## Low Risk

### [L-01] Open TODO

Open TODO can point to architecture or programming issues that still need to be resolved.

```solidity
File: contracts/Pool.sol

18    // TODO: set proper address before deployment
```

### [L-02] Remove testing function

```solidity
File: contracts/Exchange.sol

134    // temporary function for testing
135    function updateDomainSeparator() external {
136        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
137            name              : NAME,
138            version           : VERSION,
139            chainId           : block.chainid,
140            verifyingContract : address(this)
141        }));
142    }
```

## Non-critical

### [N‑01] `require` without custom errors

Use custom errors to give the idea what was the cause of failure, [source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

```solidity
File: contracts/Exchange.sol

240        require(sell.order.side == Side.Sell);

291        require(msg.sender == order.trader);

573            require(remainingETH >= price);
```

```solidity
File: contracts/Pool.sol

45        require(_balances[msg.sender] >= amount);

48        require(success);

71        require(_balances[from] >= amount);

72        require(to != address(0));
```

### [N‑02] Unused imports

```solidity
File: contracts/Exchange.sol

4 import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

### [N‑03] Lint

Wrong identation: Use always 4 spaces

```solidity
File: contracts/Exchange.sol

108      _disableInitializers();

394          return true;

526          return false;

528          return signer == recoveredSigner;
```

Remove space:

```solidity
File: contracts/Exchange.sol

 31 \n

392 \n
```

Don't use extra parenthesis:

```solidity
File: contracts/Exchange.sol

608        return (receiveAmount);
```

### [N-04] Unused commented code lines

Remove the commented code blocks, to clarify the code

```solidity
File: contracts/Exchange.sol

282        // return (price);

174        /*
175        REFERENCE
176        uint256 executionsLength = executions.length;
177        for (uint8 i=0; i < executionsLength; i++) {
178            bytes memory data = abi.encodeWithSelector(this._execute.selector, executions[i].sell, executions[i].buy);
179            (bool success,) = address(this).delegatecall(data);
180        }
181        _returnDust(remainingETH);
182        */

486            /*
487            REFERENCE
488            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
489            */

497            /*
498            REFERENCE
499            uint8 _v, bytes32 _r, bytes32 _s;
500            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
501            v = _v; r = _r; s = _s;
502            */
```
