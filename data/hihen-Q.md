* Lack of validity checking for important incoming parameters 
  - [Exchange.sol - initialize](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L112-L117): `_executionDelegate`, `_policyManager`, `_oracle` should be none-zero address.
  - [Exchange.sol - bulkExecute](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L168): `executions` should be of length greater than 0.
* Useless test code is not removed: [Exchange.sol - updateDomainSeparator](https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L134-L142)
  ```
  // temporary function for testing
  function updateDomainSeparator() external {
      DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
          name              : NAME,
          version           : VERSION,
          chainId           : block.chainid,
          verifyingContract : address(this)
      }));
  }
  ```
