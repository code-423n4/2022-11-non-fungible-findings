## Incorrect comment

File: `Exchange.sol` [Line 290-291](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L290-L291)

The comment states that the code below it is "asserting" when it is instead "requiring"

```
        // Replace "Assert" with "Require"
290:        /* Assert sender is authorized to cancel order. */
291:        require(msg.sender == order.trader);
```

## Typos

File: [`ExecutionDelegate.sol`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/ExecutionDelegate.sol) ([Line 53](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/ExecutionDelegate.sol#L53), [Line 61](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/ExecutionDelegate.sol#L61))

```
File: contracts/ExecutionDelegate.sol

/// @audit Change "on-behalf" to "on behalf"
53:     * @dev Block contract from making transfers on-behalf of a specific user

/// @audit Change "on-behalf" to "on behalf"
61:     * @dev Allow contract to make transfers on-behalf of a specific user

```

File: [`ERC1967Proxy.sol`](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/lib/ERC1967Proxy.sol) ([Line 19](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/lib/ERC1967Proxy.sol#L19))

```
File: contracts/lib/ERC1967Proxy.sol

/// @audit "initializating"
19:     * function call, and allows initializating the storage of the proxy like a Solidity constructor.

```

## Use `uint256` instead of `uint`

Making the size of the data explicit reminds the developer and the reader how much data can be stored, which may help prevent or detect bugs.

_There are 2 instances of this issue:_

File: `IExchange.sol` [Line 17](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/interfaces/IExchange.sol#L17)

```
File: contracts/interfaces/IExchange.sol

17:        uint _blockRange
```

File: `Exchange.sol` [Line 116](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L116)

```
File: contracts/Exchange.sol

116:        uint _blockRange
```

## Use `delete` to reset variables

The `delete` keyword better conveys what you are trying to do.

For example:

File: `Exchange.sol` [Line 44](https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L44)

```
        remainingETH = 0;
```

Instead of the code above, you can refactor it to the following:

```
        delete remainingETH;
```

The code above resets the variable `remainingETH` to its default value. For `uint256` that would be 0
