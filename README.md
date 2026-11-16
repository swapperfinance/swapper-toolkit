# Swapper AI Agent Toolkit

Swapper helps perp DEXs, prediction markets and other onchain apps onboard new users faster and turn more of them into funded accounts.

## Now for your AI agent

Before an agent can trade, place a bet, or open a position, its account needs funds. This toolkit gives your agent a way to fund it: the agent generates a Swapper deposit link, you confirm it, and the funds arrive where the agent needs them.

Works with [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Cursor](https://cursor.com), [Windsurf](https://windsurf.com), [OpenClaw](https://openclaw.com), [GitHub Copilot](https://copilot.github.com), [CrewAI](https://crewai.com), [AutoGPT](https://autogpt.net), and any AI agent framework that supports the open skills standard.

Powered by [Chainlink CRE](https://chain.link/cre), [Chainlink CCIP](https://chain.link/cross-chain), and [Mastercard](https://mastercard.com).

## Installation

```bash
npx skills add swapperfinance/swapper-toolkit
```

## Available Skills

### `/swapper-deposit` — Direct Deposit

Deposit and bridge funds into wallets and trading accounts. Card, wallet, or crypto transfer — straight to the address where the funds are needed.

- Direct deposits to any wallet address
- Cross-chain bridge transfers via Chainlink CCIP
- Account funding — top up a trading account or wallet before a trade, bet, or position
- Fiat on-ramp — Mastercard, Visa, Apple Pay, Google Pay (170+ countries)

**Triggers when:**
- User asks to deposit, fund, top-up, or bridge assets
- Agent detects a wallet has insufficient funds mid-task
- User wants to convert fiat to crypto and deposit into DeFi
- User wants to fund a wallet before executing a strategy

**Example:**

```
User: "Open a $100 ETH long on Hyperliquid"

Agent: the account has no USDC on Hyperliquid, so it needs funds first.

Swapper Deposit Skill:
→ Wallet: 0x2A01...c28A
→ Network: Hypercore (hypercore)
→ Token: USDC
→ Opening deposit link...

✓ Deposit link generated. Confirm in your browser. Once the funds arrive, the agent opens the position.
```

**Supported networks** include Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain, Avalanche, HyperEVM, Monad, Solana, Bitcoin, Tron, Hypercore and more. Full list: https://swapper.finance/api/v1/chains

Bitcoin and Tron work as payment sources: users can pay from them (crypto transfer via QR), but they can't be the deposit destination.

### `/swapper-trade` — Token Swap *(Coming Soon)*

Swap tokens across chains and DEXs. Cross-chain swaps via Chainlink CCIP.

- Same-chain swaps across major DEXs
- Cross-chain swaps via CCIP
- Optimal route finding
- Slippage protection

### `/swapper-wallet` — Wallet Management *(Coming Soon)*

Create, fund, and manage agent wallets.

- Smart wallet creation with account abstraction
- Multi-chain wallet setup
- Wallet funding via fiat or crypto
- Balance checking

## SDK Integration

For developers building apps who want to embed the deposit flow:

```bash
npm i @swapper-finance/deposit-sdk
```

```javascript
import { openSwapperModal } from "@swapper-finance/deposit-sdk";

openSwapperModal({
  integratorId: "your-integrator-id",
  dstChainId: "8453",
  dstTokenAddr: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  depositWalletAddress: "0xYourWalletAddress",
  styles: { themeMode: "dark" },
  supportedDepositOptions: ["transferCrypto", "depositWithCash", "walletDeposit"],
});
```

`supportedDepositOptions` sets which funding methods the widget shows. Without it, the widget shows the three defaults. A list **replaces** the defaults, so include every method you want to keep:

| Option                  | Default | What it does                                                        |
|-------------------------|---------|---------------------------------------------------------------------|
| `transferCrypto`        | on      | Crypto transfer to a deposit address (QR), from any wallet or exchange, including Bitcoin, Solana and Tron |
| `depositWithCash`       | on      | Card / fiat on-ramp                                                 |
| `walletDeposit`         | on      | Payment from a connected wallet                                     |
| `depositFromPolymarket` | off     | The user's balance on Polymarket                                    |
| `depositFromPerps`      | off     | The user's balance on a perps venue (Hyperliquid, Lighter)          |

`integratorId` is optional. Get your own at https://swapper.finance/dashboard: it works right away, and Swapper verifies new projects (integrator-fee payouts start after verification).

## Safety

- **Explicit confirmation** — every transaction requires user approval
- **Transparent fees** — slippage, gas, and risks surfaced before confirmation
- **Key security** — private keys are never stored or accessed
- Transactions are never auto-approved

## Powered by

- **[Chainlink CRE](https://chain.link/cre)** — End-to-end workflow orchestration for deposits, compliance, and settlement
- **[Chainlink CCIP](https://chain.link/cross-chain)** — Cross-chain interoperability across 60+ blockchains
- **[Mastercard](https://mastercard.com)** — Global card payment processing (170+ countries)

## Documentation

- [Docs](https://docs.swapper.finance)
- [Docs for AI agents (llms.txt)](https://docs.swapper.finance/llms.txt)
- [Swapper Finance](https://swapper.finance)

## Related

- [`@swapper-finance/deposit-sdk`](https://www.npmjs.com/package/@swapper-finance/deposit-sdk) — TypeScript SDK for embedding the deposit widget, with a typed client for the Swapper API v1
- [`@swapper-finance/orderly-universal-deposits`](https://www.npmjs.com/package/@swapper-finance/orderly-universal-deposits) — Universal Deposits plugin for Orderly DEXes

## License

[MIT](LICENSE)