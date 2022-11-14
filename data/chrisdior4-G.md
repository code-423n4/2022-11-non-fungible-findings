# [G-01]  BYTES CONSTANT ARE CHEAPER THAN STRING CONSTANTS

If the string can fit into 32 bytes, then bytes32 is cheaper than string. string is a dynamically sized-type, which has current limitations in Solidity compared to a statically sized variable. This means extra gas spent upon deployment and every time the constant is read.

- `string` public constant NAME = "Exchange";
- `string` public constant VERSION = "1.0";


Consider changing them to:

- `bytes32` constant public NAME = 'Exchange';
- `bytes32` constant public VERSION = '1.0';

Lines of code: 

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L70
- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L71

# [G-02] x += y COSTS MORE GAS THAN  x = x + y FOR STATE VARIABLES

Using the addition operator instead of plus-equals saves 113 gas:

nonces[msg.sender] += 1;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L316
==============
 totalFee += fee;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L601
===============

  _balances[msg.sender] += msg.value;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L36
===============
_balances[from] -= amount;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L73
===============

   _balances[to] += amount;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L74




# [G-03] Don't initialize variables with default value

I know this is an automated finding from C4 tool but you have missed that one:

 `bool` public isInternal = `false`;

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L146