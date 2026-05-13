---
name: aerodrome-v3-checker
description: Check Aerodrome Slipstream (V3) concentrated liquidity positions, staking status, and earnings.
tags: [defi, aerodrome, liquidity, base]
---

# Aerodrome V3 Checker

This skill allows you to check Aerodrome Slipstream (V3) concentrated liquidity positions for any wallet address.

## Core Components
- **Non-fungible Position Manager**: `0x8279226861907906221b3D1edC31F2f921dE5b72`
- **Voter (Staking Check)**: `0x16613524e02ad97eDfeF371bC883F2F5d6C480A5`

## Workflow
1. **Find NFTs**: Call `balanceOf(wallet)` on the Position Manager to see how many NFTs they have.
2. **List NFTs**: Use `tokenOfOwnerByIndex(wallet, index)` to get the specific `tokenId`s.
3. **Check Position Details**: Call `positions(tokenId)` on the Position Manager. This returns:
   - `token0`, `token1`, `tickLower`, `tickUpper`, `liquidity`
   - `feeGrowthInside0LastX128`, `feeGrowthInside1LastX128`
   - `tokensOwed0`, `tokensOwed1`
4. **Check Staking Status**: Call `ownerOf(tokenId)` on the Position Manager. If the owner is the **Voter** or a **Gauge** contract, the position is staked and earning AERO.
5. **Check Range**: Call `slot0()` on the specific pool address to get the `currentTick`. If `tickLower <= currentTick < tickUpper`, the position is In-Range.
6. **Calculate Earnings (Precise)**:
   - **Uncollected Fees**: `tokensOwed0` and `tokensOwed1` from the `positions` call.
   - **Accrued Fees (Inside Range)**: To find fees earned but not yet "owed" (synced), calculate the delta between the pool's `feeGrowthGlobal` and the position's `feeGrowthInsideLast`.
   - **Rewards**: If staked, check the Gauge contract for `earned(tokenId)`.

## Reporting Format
- **Pool**: Token0/Token1 (Fee Tier)
- **NFT ID**: #ID
- **Liquidity**: Amount in units
- **Status**: Staked/Unstaked
- **Range**: In-Range / Out-of-Range (Current Tick vs Boundaries)
- **Earnings**: Accumulated Fees (Token0/Token1) and Unclaimed AERO (if staked)
