Mikado-NFT-Staking.

Staking Contract For Mikado Hub NFT Staking, that rewards stakers with MITO.

-> Overview.

This contract allows users to stake their Mito NFTs and earn MITO tokens as rewards over time.

Different NFT token types (bronze, silver, gold) have different reward rates.

-> Features.

Stake Mito NFTs securely.
Earn rewards in MITO based on staking duration.
Unstake NFTs anytime and claim accumulated rewards.
Reward rates are customizable per NFT token type.


-> Contract Details.

Solidity version: ^0.8.0

Interfaces for Mito NFT (IMitoNFT) and MITO token (IMitoToken)

Reward rates (per second):

Bronze: 0.277 MITO

Silver: 0.555 MITO

Gold: 0.833 MITO

-> Usage.

Deploy the contract with the addresses of the Mito NFT contract and MITO token contract.
Call stake(tokenId) to stake your NFT.
Call unstake(tokenId) to withdraw your NFT and claim rewards.
Check rewards with calculateRewards(tokenId).

-> Notes.

Only the NFT owner can stake the NFT. 
**Initial** MITO tokens are transferred as rewards on unstaking. 
Make sure the contract has sufficient MITO balance to pay rewards.
