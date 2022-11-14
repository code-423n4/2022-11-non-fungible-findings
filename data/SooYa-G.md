## Replace modifier with function
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L48-L51
Modifier make code more elegant, but cost more than normal function. Since `internalCall()` modifier in Exchange.sol only called once, so its better to replace it to normal function to save gas. 

I recommend to remove the modifier and add line code below in `_execute()` function:
```require(isInternal, "This function should not be called directly");```

## STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS (3 INSTANCES)
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.
Note that one slot is equal to 32 bytes.
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L69-L86

```
    /* Constants */
    string public constant NAME = "Exchange";
    string public constant VERSION = "1.0";
    uint256 public constant INVERSE_BASIS_POINT = 10_000;
    address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
    address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;


    /* Variables */
    IExecutionDelegate public executionDelegate;
    IPolicyManager public policyManager;
    address public oracle;
    uint256 public blockRange;


    /* Storage */
    mapping(bytes32 => bool) public cancelledOrFilled;
    mapping(address => uint256) public nonces;
```

I suggest to change it into :

```
    /* Constants */
    string public constant NAME = "Exchange";
    string public constant VERSION = "1.0";
    uint256 public constant INVERSE_BASIS_POINT = 10_000;
    address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
    address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;
    /* Variables */
    IExecutionDelegate public executionDelegate;
    IPolicyManager public policyManager;
    address public oracle;
    uint256 public blockRange;
    /* Storage */
    mapping(bytes32 => bool) public cancelledOrFilled;
    mapping(address => uint256) public nonces;
```

## `<X> = <X> + <Y>` is cheaper than `<X> += <Y>`
Usually does not work with struct and mapping, but it works with state variable. 
There are some instances in this issue:
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L601
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L574
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L74
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L36

I suggest to change the code below:
```remainingETH -= price;```
to:
```remainingETH = remainingETH - price;```

## Add `unchechked{}` for substraction where the operands cannot underflow because of a previous `require` statement
Solidity >0.8.0 will always check the overflow or underflow in every operation, and it will cost an extra gas for the check. Adding `unchecked{}` for operations that cannot underflow or overflow saves gas.

```require(a <= b); x = b - a``` => ```require(a <= b); unchecked { x = b - a }```

There are some instances in this issue:
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L607
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L46
https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Pool.sol#L73
