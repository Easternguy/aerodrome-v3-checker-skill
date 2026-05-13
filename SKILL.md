---
name: aerodrome-v3-checker
description: Check Aerodrome Slipstream (V3) concentrated liquidity positions, staking status, and earnings.
version: 1.0.0
---

# Aerodrome V3 Checker

This skill allows you to check Aerodrome Slipstream (V3) concentrated liquidity positions for any wallet address.

## Core Components
- **Non-fungible Position Manager**: `0x8279226861907906221b3D1edC31F2f921dE5b72`
- **Voter (Staking Check)**: `0x16613524e02ad97eDfeF371bC883F2F5d6C480A5`

## Workflow
1. **Find NFTs**: Call `balanceOf(wallet)` on the Position Manager to see how many NFTs they have.
2. **List NFTs**: Use `tokenOfOwnerByIndex(wallet, index)` to get the specific `tokenId`s.
3. **Check Position Details**: Call `positions(tokenId)` on the Position Manager.
4. **Check Staking Status**: Call `ownerOf(tokenId)` on the Position Manager. If the owner is the **Voter** or a **Gauge** contract, the position is staked.
5. **Check Range**: Call `slot0()` on the specific pool address to get the `currentTick`.
6. **Calculate Earnings**:
   - **Fees**: `tokensOwed0` and `tokensOwed1` from the `positions` call.
   - **Rewards**: If staked, check the Gauge contract for `earned(tokenId)`.

## Reporting Format
- **Pool**: Token0/Token1 (Fee Tier)
- **NFT ID**: #ID
- **Liquidity**: Amount in units
- **Status**: Staked/Unstaked
- **Range**: In-Range / Out-of-Range
- **Earnings**: Accumulated Fees and Unclaimed AERO (if staked)
