
# Exchange.sol "temporary function" is still in the code, allowing anyone to set DOMAIN_SEPARATOR

`temporary function for testing` was left in.
This will allow anyone to set DOMAIN_SEPARATOR.
Not sure if this was left in for the purposes of code4rena, but it still needs highlighting in an audit.



---------------------

# Exchange.sol _execute() - only checks sell order side, no check for buy side

There is a check for require(sell.order.side == Side.Sell);
But no check for the other side require(buy.order.side == Side.Buy);


--------------------
# Exchange.sol _execute() - unneeded comment in last line

Comment in final line of the function can be removed `// return (price);`



---------------------

# Exchange.sol incrementNonce() - incorrect comment
Incorrect comment (It should be for cancelOrders() instead)
---------------------

---------------------
# Pool.sol - left in todo 
There is a TODO comment left in "TODO: set proper address before deployment".


-----------------
# Exchange.sol _transferTo() should check to address is not address(0) for non ETH transfers too, to avoid losing token transfers.
Only checks `require(to != address(0)` if transferring ETH.
Should include this for POOL/WETH transfers too, to avoid transfering tokens to 0 address and losing funds.


-----------------
# Exchange.sol _executeTokenTransfer() - incorrect comment
Comment above the function is missing documentation about `amount` param, but all others are listed.


-----------------
# package.json dependancies (not sure if covered by audit)
There are also some minor issues with the test files. Exchange.sol/Pool.sol were the only files to audit, but these sort of relate to the test files, not sure if worth mentioning for this audit:

the following are in package.json but not used:
ts-essentials 
ethereumjs-util 
ethereumjs-abi
nomiclabs/hardhat-web3
nomiclabs/ethereumjs-vm
ethersproject/abstract-provider
eth-optimism/smock (version 1 is deprecated anyway - should update to v2)

typechain
ts-generator
solhint-plugin-prettier
solhint
prettier-plugin-solidity
husky
fs-extra
ethereum-waffle
eslint-plugin-prettier
eslint-config-prettier
cz-conventional-changelog
commitizen
@typescript-eslint/parser
@typescript-eslint/eslint-plugin
ethersproject/bytes
ethersproject/bignumber
ethersproject/abstract-signer
ethersproject/abi
commitlint/config-conventional
commitlint/cli
codechecks/client

tests/exchange/setup.ts imports unused ZERO_ADDRESS
			also setupMocks does not use bob

and ganache-cli is now ganache

