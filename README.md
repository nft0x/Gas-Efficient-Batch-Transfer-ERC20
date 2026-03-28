# Gas-Efficient-Batch-Transfer-ERC20
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract BatchTransfer is ReentrancyGuard, Ownable {
    IERC20 public token;

    event BatchSent(uint256 totalRecipients, uint256 totalAmount);

    constructor(address _token, address initialOwner) Ownable(initialOwner) {
        token = IERC20(_token);
    }

    function batchTransfer(address[] calldata recipients, uint256[] calldata amounts) external nonReentrant {
        require(recipients.length == amounts.length, "Length mismatch");
        uint256 total = 0;
        for (uint256 i = 0; i < recipients.length; i++) {
            token.transferFrom(msg.sender, recipients[i], amounts[i]);
            total += amounts[i];
        }
        emit BatchSent(recipients.length, total);
    }

    function rescueTokens(address _token, uint256 amount) external onlyOwner {
        IERC20(_token).transfer(owner(), amount);
    }
}
