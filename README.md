# Mikado-NFT-Staking.
Staking Contract For Mikado Hub NFT Staking.

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IMitoNFT {
    function safeTransferFrom(address from, address to, uint256 tokenId) external;
    function ownerOf(uint256 tokenId) external view returns (address);
}

interface IMitoToken {
    function transfer(address to, uint256 amount) external returns (bool);
}

contract MitoStaking {
    IMitoNFT public nft;
    IMitoToken public mitoToken;
    address public owner;

    struct StakeInfo {
        address staker;
        uint256 tokenId;
        uint256 startTime;
    }

    mapping(uint256 => StakeInfo) public stakes; // tokenId => stake details
    mapping(uint256 => uint256) public rewardRates; // tokenId => reward/sec

    event Staked(address indexed user, uint256 indexed tokenId, uint256 time);
    event Unstaked(address indexed user, uint256 indexed tokenId, uint256 time, uint256 reward);
    event RewardClaimed(address indexed user, uint256 indexed tokenId, uint256 reward);

    constructor(address _nft, address _mitoToken) {
    nft = IMitoNFT(_nft);
    mitoToken = IMitoToken(_mitoToken);
    owner = msg.sender;

    rewardRates[0] = 277777777777777777; // bronze
    rewardRates[1] = 555555555555555555; // silver
    rewardRates[2] = 833333333333333333; // gold
}

    function stake(uint256 tokenId) external {
        require(nft.ownerOf(tokenId) == msg.sender, "Not owner");
        nft.safeTransferFrom(msg.sender, address(this), tokenId);

        stakes[tokenId] = StakeInfo({
            staker: msg.sender,
            tokenId: tokenId,
            startTime: block.timestamp
        });

        emit Staked(msg.sender, tokenId, block.timestamp);
    }

    function unstake(uint256 tokenId) external {
        StakeInfo memory info = stakes[tokenId];
        require(info.staker == msg.sender, "Not staker");

        uint256 rewards = calculateRewards(tokenId);
        delete stakes[tokenId];

        nft.safeTransferFrom(address(this), msg.sender, tokenId);

        require(mitoToken.transfer(msg.sender, rewards), "MITO transfer failed");

        emit Unstaked(msg.sender, tokenId, block.timestamp, rewards);
        emit RewardClaimed(msg.sender, tokenId, rewards);
    }

    function calculateRewards(uint256 tokenId) public view returns (uint256) {
        StakeInfo memory info = stakes[tokenId];
        if (info.startTime == 0) return 0;

        uint256 duration = block.timestamp - info.startTime;
        return duration * rewardRates[tokenId];
    }
}
