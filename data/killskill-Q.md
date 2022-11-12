@openzeppelin/contracts
Improper Verification of Cryptographic Signature

Detailed paths and remediation
Introduced through: package.json@1.0.0 › @openzeppelin/contracts@4.4.1
Fix: Upgrade to @openzeppelin/contracts@4.7.3 

@openzeppelin/contracts is a library for contract development.
Affected versions of this package are vulnerable to Improper Verification of Cryptographic Signature via ECDSA.recover and ECDSA.tryRecover due to accepting EIP-2098 compact signatures in addition to the traditional 65 byte signature format.

-------------------------------------------------------------------------------------------------------------------------------------------------------

@openzeppelin/contracts
Incorrect Calculation

Detailed paths and remediation
Introduced through: package.json@1.0.0 › @openzeppelin/contracts@4.4.1
Fix: Upgrade to @openzeppelin/contracts@4.7.2 

@openzeppelin/contracts is a library for contract development.

Affected versions of this package are vulnerable to Incorrect Calculation via the GovernorVotesQuorumFraction module. This vulnerability is exploitable by passing a proposal to lower the quorum requirements, leading to past proposals possibly becoming executable if they had been defeated only due to lack of quorum, and the number of votes it received meets the new quorum requirement.

---------------------------------------------------------------------------------------------------------------------------------------------------------

@openzeppelin/contracts
Information Exposure

Detailed paths and remediation
Introduced through: package.json@1.0.0 › @openzeppelin/contracts@4.4.1
Fix: Upgrade to @openzeppelin/contracts@4.7.1

@openzeppelin/contracts is a library for contract development.

Affected versions of this package are vulnerable to Information Exposure. SignatureChecker.isValidSignatureNow is not expected to revert. However, an incorrect assumption about Solidity 0.8's abi.decode allows some cases to revert, given a target contract that doesn't implement EIP-1271 as expected.

The contracts that may be affected are those that use SignatureChecker to check the validity of a signature and handle invalid signatures in a way other than reverting. We believe this to be unlikely.
