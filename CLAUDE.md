# Swapper AI Agent Toolkit

This repository contains the Swapper AI Agent Toolkit — skills that give AI agents the ability to deposit funds, swap tokens, and manage wallets in DeFi.

## What is Swapper Finance?

Swapper Finance is the payment infrastructure layer for Web3. It lets users and agents deposit funds into DeFi protocols via payment cards (Mastercard, Visa, Apple Pay, Google Pay), crypto transfers, and wallet connections. Supports 170+ countries. Cross-chain via Chainlink CCIP. End-to-end orchestration via Chainlink CRE. Card processing via Mastercard.

## Repository structure

```
.claude/
  settings.json       # Skill registration
skills/
  swapper-deposit/    # Deposit and bridge funds into a wallet
    SKILL.md          # Skill definition (read by agents)
CLAUDE.md             # This file (read by Claude Code automatically)
README.md             # Repository overview
package.json          # Skill pack metadata
```

## How the deposit skill works

1. Agent calls `/swapper-deposit` with wallet address, chain, token
2. Skill generates a Swapper deposit deeplink with correct parameters
3. Deeplink opens in user's browser
4. User completes deposit via Swapper widget (card, transfer, or wallet)
5. Funds land at `depositWalletAddress` on the target chain

The skill does NOT execute transactions — it generates a link. The user always confirms.

## Deeplink format

```
https://deposit.swapper.finance?integratorId=d6e438dfa14e80709b19&dstChainId=CHAIN_ID&dstTokenAddr=TOKEN_ADDR&depositWalletAddress=WALLET_ADDR&utm_source=swapper-deposit&extendedView=true
```

- `integratorId` is always `d6e438dfa14e80709b19` (hardcoded)
- For native tokens on EVM chains (ETH, POL, BNB, etc.), use `0x0000000000000000000000000000000000000000` as token address. Other networks have their own native token address (see below)

## Supported chains

Supported networks include Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain, Avalanche, HyperEVM, Monad, Solana, Bitcoin, Tron, Hypercore and more. Full list: https://swapper.finance/api/v1/chains

Deposit destinations (`dstChainId`):

| Chain       | Chain ID       | Native token address                          |
|-------------|----------------|-----------------------------------------------|
| Ethereum    | `1`            | `0x0000000000000000000000000000000000000000`  |
| Base        | `8453`         | `0x0000000000000000000000000000000000000000`  |
| Arbitrum    | `42161`        | `0x0000000000000000000000000000000000000000`  |
| Optimism    | `10`           | `0x0000000000000000000000000000000000000000`  |
| Polygon     | `137`          | `0x0000000000000000000000000000000000000000`  |
| BNB Chain   | `56`           | `0x0000000000000000000000000000000000000000`  |
| Avalanche   | `43114`        | `0x0000000000000000000000000000000000000000`  |
| HyperEVM    | `999`          | `0x0000000000000000000000000000000000000000`  |
| Monad       | `143`          | `0x0000000000000000000000000000000000000000`  |
| Solana      | `mainnet-beta` | `So11111111111111111111111111111111111111111` |
| Hypercore   | `hypercore`    | `0x6d1e7cde53ba9467b783cb7c530ce054` (USDC)   |
| Fast        | `fast`         | not in the API                                |

- Fast is supported by the deposit widget only (not by the Swapper API). Its `depositWalletAddress` must be a Fast address (`fast1…`); `0x…` addresses are rejected.
- Bitcoin and Tron are source networks only: users can pay from them, but they can't be a deposit destination.

## Safety rules

- Every transaction requires explicit user confirmation
- Never auto-approve transactions
- Always surface fees and risks before confirmation
- Never store or access private keys
- Always display the full deeplink URL so user can verify