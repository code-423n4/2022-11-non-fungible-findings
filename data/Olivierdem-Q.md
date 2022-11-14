QA Report

    1. TODO left in the code (found line 18 in Pool.sol)

    2. As of right now, EXCHANGE and SWAP addresses in Pool.sol are both set as "address private constant", and are both given the same address (i.e. 0x707531c9999AaeF9232C8FEfBA31FBa4cB78d84a), but there is a 
    TODO left in the code ("// TODO: set proper address before deployment") right above SWAP which leads to believe that SWAP will be set to a different address than EXCHANGE.
    BUT, if that's the case, the if statement in the function transferFrom in Pool.sol will always fail since there is the line " if (msg.sender != EXCHANGE && msg.sender != SWAP) {".
    Mitigation: Only use one var for EXCHANGE and SWAP or use '||' instead of '&&' in the if statement

    3. Code leftover. There is a return(price) statement line 282 in exchange.sol that is commented out and should be removed