***→ A modifier should only check conditions and never change the state, but in this modifier these two properties are not considered. 

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L40



_________________________________________________________________

***→ There are six index event parameters in Exchange.sol contract. But there is a limit that only up to 3 parameters can be indexed. Indexing parameters are cost-efficient and will take more gas fees for the transaction to be mined. 

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L91

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L92

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L100

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L102

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L103

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L104

_____________________________________________________________________



***→Based on the comment in line above this function; This is a temporary function for testing, but it seems that you forgot to delete or remove the function. This function is without modifier and public, and deploying it with the main contract may cause problems

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L134

_____________________________________________________________________
   


***→ The contract does not include a mechanism to check that the address of the seller and the buyer are not the same.  An EOA or contract may trade with itself to manipulate the price of an asset or fee.
_______________________________________________________________



***→ All  below require lines need an explanation (comment field) in the form of a comment or a Custom error.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L573

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L240

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L291

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L48


______________________________________________________________



***→ By calling or not calling this function, the user can manipulate the Nounce  variable. Because it is an informal indicator of the number of transactions by a user, intentional or accidental manipulation of this index conveys wrong information to others and has an effect on the hash calculation too. Although it has an effect on gas, it is recommended  to calculate this index through the functions of buying or selling (cancelling is also included) and not by the user.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L315

_____________________________________________________________________




https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L414

***→ It is necessary to add the above condition check to the following two validation  processes in the same function.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L398

And:

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L393

______________________________________________________________________



***→ This function need a require condition check  and send a custom error message to user if AssetType is not ERC721 type and not ERC1155 too.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L653

_____________________________________________________________________

***→ Using the zero address (address(0)) in the” to” address field of this “emit” confuse users. They may assume that the tokens are sent to zero address to be burned.  Using the pool address here is more useful.

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L37

________________________________


***--> Programming style affects readability and maintainability. There are style guides and  guidelines that are not followed in exchange.sol contract.

Type declarations
State variables
Events
Modifiers
Functions


______________________________________________________


Thank you 
🙂
