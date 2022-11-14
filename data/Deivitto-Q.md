
# QA
# Low
## Missing checks for address(0x0) when assigning values to `address` state variables 
### Summary
Zero address should be checked for state variables. A not expected zero address can lead into problems, for example trying to use 0x0 as `oracle`.
### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L128-L130
### Mitigation
Check zero address before assigning or using it


## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
- `block.timestamp` used in comparisons
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L377
            (order.listingTime < block.timestamp) &&

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L378
            (block.timestamp < order.expirationTime)

### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision

# Informational

## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
- without indexed params
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L99
    event OrderCancelled(bytes32 hash);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L105
    event NewBlockRange(uint256 blockRange);

- missing any kind of data to be emitted
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L53
    event Opened();

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L54
    event Closed();
### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.




## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots

### Github Permalink
- modifiers, variables, events, functions, all disordered
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L33-L147

- event variables and functions disordered
https://github.com/code-423n4/2022-11-non-fungible/blob/835e8191e811d6813245cce99a6e5e456448111e/contracts/Pool.sol#L15-L23

### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html




## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
#### Natspec @param and/or @return value
https://github.com/code-423n4/2022-11-non-fungible/blob/835e8191e811d6813245cce99a6e5e456448111e/contracts/Pool.sol#L57-L86
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L322-L356
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L111-L132
#### Missing @return
- 7 instances of functions with return but no @return at
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L1-L668
 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does

## Missing initializer modifier on constructor
### Impact
OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5) that the initializer modifier be applied to constructors in order to avoid potential griefs, [social engineering](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/4), or exploits. 

### Github Permalink
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L107-L109
### Mitigation
Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.





## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a `TODO` that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks


https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L18
    // TODO: set proper address before deployment


### Mitigation
Remove `TODO`

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L230

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L313

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L500

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L546

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L550


### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 


## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 

### Github Permalinks
https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L537-L555

### Mitigation
Remove return or returns when both used



## Missing error messages in require statements
### Summary
Require/revert statements should include error messages in order to help at monitoring the system.
### Github Permalinks


https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L573
            require(remainingETH >= price);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L45
        require(_balances[msg.sender] >= amount);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L48
        require(success);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L71
        require(_balances[from] >= amount);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Pool.sol#L72
        require(to != address(0));

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L291
        require(msg.sender == order.trader);

https://github.com/code-423n4/2022-11-non-fungible/blob/8eaeb007b81d3c89ecf6a5dc2a67eecbe94cefaf/contracts/Exchange.sol#L240
        require(sell.order.side == Side.Sell);

### Mitigation
Add error messages 
