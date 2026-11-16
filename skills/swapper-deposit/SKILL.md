---
name: swapper-deposit
user-invocable: true
description: >
  Deposit and bridge funds into a wallet using Swapper Finance.
  Handles fiat on-ramp (card payments via Mastercard, Visa, Apple Pay, Google Pay),
  crypto transfers, and cross-chain bridging via Chainlink CCIP. Supported networks
  include Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain, Avalanche,
  HyperEVM, Monad, Solana, Bitcoin, Tron, Hypercore and more.
  170+ countries supported. Powered by Chainlink CRE and Mastercard.
  Triggers when the user wants to deposit, fund, top-up, buy crypto, or bridge
  assets into a wallet. Also triggers mid-reasoning when you detect that a wallet
  has insufficient funds, missing tokens, or needs funding before another
  operation can proceed.
---

# Swapper Deposit Skill

## When to activate

- The user explicitly asks to deposit, fund, top-up, or bridge assets into a wallet.
- Mid-reasoning: you detect that a wallet lacks sufficient funds to complete a requested operation (e.g., a trade, deployment, or transfer fails due to insufficient balance).

## What you need from the user

Collect the following before generating the deeplink. Ask for any missing values:

| Parameter              | Required | Description                                          | Example                                        |
|------------------------|----------|------------------------------------------------------|------------------------------------------------|
| `depositWalletAddress` | Yes      | The wallet address that will receive the deposit     | `0x2A018F2506acaEEE2C10632514Fc5DCa9eE2c28A`  |
| `dstChainId`           | Yes      | Destination chain ID                                 | `8453` (Base)                                  |
| `dstTokenAddr`         | Yes      | Destination token address (ERC-20 on EVM, mint on Solana) | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`  |

**Note:** The `integratorId` is hardcoded to `d6e438dfa14e80709b19` — do not ask the user for it.

**Native tokens (gas tokens):** On EVM chains (ETH, POL, BNB, AVAX, HYPE, MON, etc.), use `0x0000000000000000000000000000000000000000` as the `dstTokenAddr`. Other networks have their own native token address — see the table below.

### Common chain IDs

| Chain     | `dstChainId`   | Native token `dstTokenAddr`                    |
|-----------|----------------|------------------------------------------------|
| Ethereum  | `1`            | `0x0000000000000000000000000000000000000000`   |
| Base      | `8453`         | `0x0000000000000000000000000000000000000000`   |
| Arbitrum  | `42161`        | `0x0000000000000000000000000000000000000000`   |
| Optimism  | `10`           | `0x0000000000000000000000000000000000000000`   |
| Polygon   | `137`          | `0x0000000000000000000000000000000000000000`   |
| BNB Chain | `56`           | `0x0000000000000000000000000000000000000000`   |
| Avalanche | `43114`        | `0x0000000000000000000000000000000000000000`   |
| HyperEVM  | `999`          | `0x0000000000000000000000000000000000000000`   |
| Monad     | `143`          | `0x0000000000000000000000000000000000000000`   |
| Solana    | `mainnet-beta` | `So11111111111111111111111111111111111111111`  |
| Hypercore | `hypercore`    | `0x6d1e7cde53ba9467b783cb7c530ce054` (USDC)    |
| Fast      | `fast`         | see note below                                 |

- **Solana:** use `mainnet-beta` (the ID from the Swapper API). `depositWalletAddress` is a Solana address.
- **Hypercore** (Hyperliquid): `depositWalletAddress` is the user's `0x…` Hyperliquid address. USDC is the only token.
- **Fast:** supported by the deposit widget only, not by the Swapper API. `depositWalletAddress` must be a Fast address (`fast1…`); `0x…` addresses are rejected. Fast token addresses are not listed in the API — ask the user for the token address.
- **Bitcoin and Tron** are source networks only: users can pay from them (with `transferCrypto`), but they can't be the deposit destination.

For other networks and token addresses, check `https://swapper.finance/api/v1/chains` and `https://swapper.finance/api/v1/tokens`.

## How to generate the deposit deeplink

Build the URL using this base and required query parameters:

```
https://deposit.swapper.finance?integratorId=d6e438dfa14e80709b19&dstChainId=CHAIN_ID&dstTokenAddr=TOKEN_ADDR&depositWalletAddress=WALLET_ADDR&utm_source=swapper-deposit&extendedView=true
```

### Optional parameters

- `styles` — JSON string (URL-encoded) for theming, e.g. `styles=%7B%22themeMode%22%3A%22dark%22%7D`
- `supportedDepositOptions` — JSON array (URL-encoded) of the funding methods the widget shows. See below.

#### `supportedDepositOptions`

Without it, the widget shows the three defaults. A list **replaces** the defaults, so include every method you want to keep:

| Option                  | Default | What it does                                                                        |
|-------------------------|---------|-------------------------------------------------------------------------------------|
| `transferCrypto`        | on      | Crypto transfer to a deposit address (QR), from any wallet or exchange, including Bitcoin, Solana and Tron |
| `depositWithCash`       | on      | Card / fiat on-ramp                                                                 |
| `walletDeposit`         | on      | Payment from a connected wallet                                                     |
| `depositFromPolymarket` | off     | The user's balance on Polymarket                                                    |
| `depositFromPerps`      | off     | The user's balance on a perps venue (Hyperliquid, Lighter)                          |

Add an opt-in source when the user's funds are already there. For example, to fund a bet or position from a Polymarket balance, keep the defaults and add `depositFromPolymarket`:

```
supportedDepositOptions=%5B%22transferCrypto%22%2C%22depositWithCash%22%2C%22walletDeposit%22%2C%22depositFromPolymarket%22%5D
```

(decoded: `["transferCrypto","depositWithCash","walletDeposit","depositFromPolymarket"]`)

### Example deeplink

```
https://deposit.swapper.finance?integratorId=d6e438dfa14e80709b19&dstChainId=8453&dstTokenAddr=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&depositWalletAddress=0x2A018F2506acaEEE2C10632514Fc5DCa9eE2c28A&utm_source=swapper-deposit&extendedView=true
```

## Example interaction

User: "I need to deposit $100 USDC into my wallet on Base"

Agent:
→ Wallet: 0x2A01...c28A (from context)
→ Chain: Base (8453)
→ Token: USDC (0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913)

Generating deposit link...

✓ https://deposit.swapper.finance?integratorId=d6e438dfa14e80709b19&dstChainId=8453&dstTokenAddr=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&depositWalletAddress=0x2A01...c28A&utm_source=swapper-deposit&extendedView=true

Opening in your browser. Please verify the token and wallet address before confirming.

---

User: "Fund my Arbitrum wallet with ETH before deploying this contract"

Agent (mid-reasoning trigger):
→ Detected: wallet 0xAB12...9f3D has 0 ETH on Arbitrum
→ Funding needed before deployment can proceed
→ Chain: Arbitrum (42161)
→ Token: ETH (0x0000000000000000000000000000000000000000)

Generating deposit link...

✓ Link generated. Fund your wallet first, then I'll continue with the deployment.

## Backed by Chainlink and Mastercard

- **Chainlink CRE** (Runtime Environment) — orchestrates the full deposit workflow: compliance, payment authorization, conversion, and settlement in a single verifiable flow
- **Chainlink CCIP** (Cross-Chain Interoperability Protocol) — routes cross-chain transfers securely across 60+ blockchains
- **Mastercard** — global card payment processing covering 170+ countries (Visa, Apple Pay, Google Pay also supported)


## Steps

1. Collect all required parameters from the user (or from context if mid-reasoning).
2. Construct the deeplink URL with the parameters.
3. **Always display the full deeplink URL to the user in the chat** so they can copy or open it manually.
4. Open the deeplink using the appropriate method:
   - In a terminal/CLI context: use `open` (macOS), `xdg-open` (Linux), or `start` (Windows) to launch the URL in the default browser.
   - Example: `start "https://deposit.swapper.finance?integratorId=...&dstChainId=...&dstTokenAddr=...&depositWalletAddress=..."`
   - **Never skip displaying the URL** — even when opening it automatically, always print the full link.
5. **Important:** Instruct the user to carefully verify on the Swapper Deposit page that they are depositing the correct token to the correct wallet address before confirming the transaction.

## SDK integration (for developers building apps)

If the user is building an app and wants to embed the deposit widget, provide the relevant SDK code:

### Install

```bash
npm i @swapper-finance/deposit-sdk
```

### Embedded container

```typescript
import { SwapperIframe } from '@swapper-finance/deposit-sdk';

const swapper = new SwapperIframe({
  container: '#swapper-container', // or HTMLElement
  integratorId: 'your-integrator-id',
  dstChainId: '8453',
  dstTokenAddr: '0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913',
  depositWalletAddress: '0x2A018F2506acaEEE2C10632514Fc5DCa9eE2c28A',
  // optional
  styles: { themeMode: 'light' },
  supportedDepositOptions: ['transferCrypto', 'depositWithCash', 'walletDeposit'],
});
```

### Modal popup

```typescript
import { openSwapperModal } from '@swapper-finance/deposit-sdk';

const modal = openSwapperModal({
  integratorId: 'your-integrator-id',
  dstChainId: '8453',
  dstTokenAddr: '0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913',
  depositWalletAddress: '0x2A018F2506acaEEE2C10632514Fc5DCa9eE2c28A',
  // optional
  styles: { themeMode: 'dark' },
  supportedDepositOptions: ['transferCrypto', 'depositWithCash', 'walletDeposit'],
  modalStyle: { borderRadius: '16px' },
  onClose: () => console.log('Closed'),
});
```

`supportedDepositOptions` works the same as the deeplink parameter (see [`supportedDepositOptions`](#supporteddepositoptions) above): a list replaces the three defaults, and `depositFromPolymarket` / `depositFromPerps` must be added explicitly.

`integratorId` is optional. Get your own at https://swapper.finance/dashboard: it works right away, and Swapper verifies new projects (integrator-fee payouts start after verification). In the SDK, use the developer's own id — the hardcoded `d6e438dfa14e80709b19` is only for deeplinks generated by this skill.

### Raw iframe embed

```html
<iframe
  src="https://deposit.swapper.finance?integratorId=YOUR_ID&dstChainId=8453&dstTokenAddr=0x...&depositWalletAddress=0x..."
  title="Swapper Deposits Widget"
  allow="camera; clipboard-write"
  sandbox="allow-forms allow-modals allow-popups allow-popups-to-escape-sandbox allow-same-origin allow-scripts allow-top-navigation-by-user-activation"
></iframe>
```

Copy the `allow` and `sandbox` attributes exactly — they are the values the SDK (`SwapperIframe`) sets, and each one is used: `camera` for identity checks during card payments, `clipboard-write` for the copy buttons (e.g. the deposit address), and `allow-top-navigation-by-user-activation` to open wallet apps on iOS. Leave one out and part of the flow breaks.

## Documentation

Full Swapper docs, indexed for LLMs: https://docs.swapper.finance/llms.txt. Every page is also available as Markdown by appending `.md` to its URL.
