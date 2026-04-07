# Automated-Reward-Distributor
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.0/contracts/token/ERC20/IERC20.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.0/contracts/access/Ownable.sol";

contract RewardDistributor is Ownable {
    IERC20 public rewardToken;
    mapping(address => uint256) public pendingRewards;

    event RewardsClaimed(address user, uint256 amount);

    constructor(address _rewardToken) Ownable(msg.sender) {
        rewardToken = IERC20(_rewardToken);
    }

    function addRewards(address[] calldata users, uint256[] calldata amounts) external onlyOwner {
        for (uint i = 0; i < users.length; i++) {
            pendingRewards[users[i]] += amounts[i];
        }
    }

    function claim() external {
        uint256 amount = pendingRewards[msg.sender];
        require(amount > 0, "No rewards");
        pendingRewards[msg.sender] = 0;
        rewardToken.transfer(msg.sender, amount);
        emit RewardsClaimed(msg.sender, amount);
    }
}
