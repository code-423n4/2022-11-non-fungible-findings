1. https://github.com/code-423n4/2022-11-non-fungible/blob/main/contracts/Exchange.sol#L184
Change 

i++

to

unchecked {
    i++;
}

to save gase.