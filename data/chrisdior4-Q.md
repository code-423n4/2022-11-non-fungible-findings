# [N-01] Events to be declared in the interface

It is best practice to declare the events inside the interface, not in the implementation. By defining events inside the interface, any contract implementation will have direct access to the events and use them to emit inside the code. Redefining the event definition in every single contract implementation can also be cumbersome. By moving the event definition inside the interface, the contract focuses more on the logic and how it does things, providing better clarity, separation of concerns and clean code.

Referring for both `Exchange.sol` & `Pool.sol`

==========================================

# [N-02] Public functions not called by the contract should be declared external instead

- function withdraw(uint256 amount) public {
        require(_balances[msg.sender] >= amount);
        _balances[msg.sender] -= amount;
        (bool success,) = payable(msg.sender).call{value: amount}("");
        require(success);
        emit Transfer(address(0), msg.sender, amount);
    }
- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Pool.sol#L44

# [N-03] Lack of Zero Address Check May Cause User Lose Asset Permanently

- function _executeTokenTransfer(
        address collection,
        address from,
        `address to`,
        uint256 tokenId,
        uint256 amount,
        AssetType assetType
    ) internal {
        /* Call execution delegate. */
        if (assetType == AssetType.ERC721) {
            executionDelegate.transferERC721(collection, from, to, tokenId);

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L653

Recommended mitigation steps:

Consider adding a check - `require(to != address(0), "Transfer to zero address");`



# [L-01] Missing event emitting 

Missing events and timelocks do not promote transparency and if such changes immediately affect users' perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation:

- function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L306

# [L-02] Use the latest version of OpenZeppelin

To prevent any issues in the future (e.g. using solely hardhat to compile and deploy the contracts), upgrade the used OZ packages within the package.json to the latest versions.

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/package.json#L61

### Recommended Mitigation Steps:

Consider using the latest OZ packages (v4.7.3) within package.json.

# [L-03] Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

uint256 public constant INVERSE_BASIS_POINT = 10_000; - 
uint256 public constant INVERSE_BASIS_POINT = 1e4; +

- https://github.com/code-423n4/2022-11-non-fungible/blob/323b7cbf607425dd81da96c0777c8b12e800305d/contracts/Exchange.sol#L72



