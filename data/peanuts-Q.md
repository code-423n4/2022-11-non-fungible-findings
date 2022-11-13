## Table of Contents

- [L-01] EIP712 DOMAIN_HASH should include a salt as well for best practice
- [L-02] Best practice is to prevent signature malleability
- [N-01] Avoid the use of sensitive terms in favour of neutral ones

### [L-01] EIP712 DOMAIN_HASH should include a salt as well for best practice
```
string name the user readable name of signing domain, i.e. the name of the DApp or the protocol.
string version the current major version of the signing domain. Signatures from different versions are not compatible.
uint256 chainId the EIP-155 chain id. The user-agent should refuse signing if it does not match the currently active chain.
address verifyingContract the address of the contract that will verify the signature. The user-agent may do contract specific phishing prevention.
bytes32 salt an disambiguating salt for the protocol. This can be used as a domain separator of last resort.
```
https://eips.ethereum.org/EIPS/eip-712#definition-of-domainseparator

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L136-L142

### [L-02]  Best practice is to prevent signature malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L524

### [N-01] Avoid the use of sensitive terms in favour of neutral ones

Use alternative variants, e.g. allowlist/denylist instead of whitelist/blacklist

https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L545-L549