1)                 ITS NOT BEST PRACTICE TO ASSIGN ANY CONTRACT ADDRESS OR ANY WALLET ADDRESS DIRECTLY TO THE VARIABLES.  INSTEAD OF ASSIGNING DIRECTLY CAN USE env FILES . IN THE IMPLEMENTATION PART WE CAN ONLY USE KEYS.

There are 4 instances of this issue:

FILE:  2022-11-non-fungible/contracts/Exchange.sol

73:             address public constant WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

74:             address public constant POOL = 0xF66CfDf074D2FFD6A4037be3A669Ed04380Aef2B;

FILE:   2022-11-non-fungible/contracts/Pool.sol

17:             address private constant EXCHANGE = 0x707531c9999AaeF9232C8FEfBA31FBa4cB78d84a;

19:             address private constant SWAP = 0x707531c9999AaeF9232C8FEfBA31FBa4cB78d84a;

---------------------------------------------------------------------------------------------------------------------------------------------

2)            LACK OF ZERO ADDRESS CHECKS FOR NEW ADDRESSES IN FUNCTIONS 

FILE:  2022-11-non-fungible/contracts/Exchange.sol

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L440-L461

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L440-L461

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L565-L582

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L565-L582

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L565-L582

---------------------------------------------------------------------------------------------------------------------------------------------------------

3)        THERE IS NO EXPLAINATIONS ABOUT  _returnDust() . THIS IS NOT GOOD CODE PRACTICE . NO COMMENTS ADDED ABOVE THE FUNCTION DEFINITION 

FILE:   2022-11-non-fungible/contracts/Exchange.sol



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
-------------------------------------------------------------------------------------------------------------------------------------------------------------

4)          REQUIRE STAEMENT NOT RETURNED ANY ERROR CODES. WITHOUT ERROR CODES DON'T KNOW WHAT ERROR OCCURS IN THE CONDITION CHECKS 

FILE:  2022-11-non-fungible/contracts/Exchange.sol

240:   require(sell.order.side == Side.Sell);


--------------------------------------------------------------------------------------------------------------------------------------------------------------

5)            WRONG COMMENTS ADDED FOR  incrementNonce() FUNCTION

FILE:  2022-11-non-fungible/contracts/Exchange.sol

/**
     * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
     */
    function incrementNonce() external {
        nonces[msg.sender] += 1;
        emit NonceIncremented(msg.sender, nonces[msg.sender]);
    }

------------------------------------------------------------------------------------------------------------------------------------------------------------

6)  


