# Forth Connect AI — Cotopia
Version 1.0 — Last updated: Oct. 2025

A sellable, developer-friendly pay-to-play game template (frontend + smart contract + deploy/test tooling). Designed for rapid customization, secure smart‑contract integration, and easy delivery to customers or marketplaces.

---

## Table of contents
- Overview
- Features
- Repository structure
- Editions & licensing
- Prerequisites
- Quickstart (5 minutes)
- Detailed setup
  - Configuration
  - Install dependencies
  - Local dev (frontend + local chain)
  - Deploy contracts (testnet / mainnet)
- Frontend integration
  - `payments.js` API
  - UI hooks
- Testing
  - Contract unit tests
  - Fuzzing / property tests
  - Frontend E2E
- CI / CD
- Packaging & delivery
- Security & audits
- Recommended production hardening
- Common pitfalls & troubleshooting
- Support & professional services
- Changelog
- Contact & legal

---

## Overview
Forth Connect AI is a ready-made game template by Cotopia that includes:
- Game UI and assets (HTML/CSS/JS)
- `payments.js` module for pay-to-play integration (ESM + UMD)
- Example Solidity contracts (PayToPlay pattern) with Hardhat/Foundry configs
- Deploy scripts, tests, CI, demo site, and documentation
- License/terms templates and privacy policy

This repo is designed to be resold as a template or used as a base for production games.

---

## Features
- Modular payments module: `init(config)` and well-documented methods
- Example ERC20 payment flow (approve -> buyPlays -> consumePlay)
- Safe token handling using OpenZeppelin `SafeERC20`
- Deploy scripts for popular chains (Ethereum, Polygon, Arbitrum, Optimism)
- Hardhat & Foundry test suites + sample audits checklist
- ESM + UMD frontend bundles (Vite/Rollup)
- CI templates (GitHub Actions) to run tests, lint, and build
- Demo site configured to run on testnets
- License keys and download management examples
- Support templates and onboarding flow

---

## Repository structure
(Top-level files and directories)
- README.md — this file
- LICENSE — license for this repo (choose MIT, proprietary, etc.)
- TERMS_OF_SALE.md — terms of sale & EULA (Cotopia)
- PRIVACY.md — privacy policy (Cotopia)
- package.json — frontend & tooling scripts
- /frontend
  - index.html
  - src/
    - app.js
    - payments.js (ESM)
    - styles.css
  - public/
- /contracts
  - PayToPlay.sol
  - interfaces/
  - tests/ (contract unit tests)
- /scripts
  - deploy.js
  - verify.js
  - migrate.js
- /hardhat / /foundry — configs and toolchain files
- /ci — GitHub Actions workflow examples
- /demo — hosted demo configuration (Vercel/Netlify)
- /support — onboarding emails, refund templates, troubleshooting docs
- /docs — developer docs, API reference
- /examples — sample integrations, plugin examples
- /assets — images, videos, sprites, screencast

---

## Editions & licensing
Decide which edition you sell and include appropriate license files:
- Lite — for demo and learning (no commercial redistribution)
- Pro — full template with deploy scripts, basic support (resell derivatives allowed per terms)
- Enterprise — custom licensing, audit assistance, white-label options

Include `LICENSE` and `TERMS_OF_SALE.md` tailored to the edition.

---

## Prerequisites
- Node.js v18+ (or the LTS you target)
- npm or yarn
- Hardhat (npx hardhat) or Foundry (optional)
- Git
- A wallet/private key for deployments (use testnet keys for dev)
- Recommended: Docker for consistent test environments

Security note: never commit private keys or `.env` files to the repo.

---

## Quickstart (5 minutes)
1. Clone repo:
   git clone <repo-url> && cd forth-connect-ai
2. Copy sample env:
   cp .env.example .env
   - Set RPC_URL, PRIVATE_KEY (testnet), INFURA/ALCHEMY keys, CHAIN_ID
3. Install:
   npm install
4. Compile contracts:
   npx hardhat compile
5. Run local dev (frontend + Hardhat node):
   npm run dev
6. Deploy to testnet (example: Goerli/Scroll/Polygon Mumbai):
   npx hardhat run scripts/deploy.js --network goerli
7. Update `frontend/config.example.json` with deployed contract & token addresses and open demo.

---

## Detailed setup

### Configuration
- `.env.example` fields:
  - RPC_URL — JSON-RPC endpoint for target network
  - PRIVATE_KEY — deployer private key (use test keys)
  - ETHERSCAN_API_KEY — for verification
  - COTOPIA_LICENSE_KEY — optional for pro features
- `frontend/config.example.json`:
  - CONTRACT_ADDRESS
  - TOKEN_ADDRESS
  - TOKEN_DECIMALS
  - CHAIN_ID
  - PRICE_PER_PLAY (in token units)

Provide a small `scripts/generateConfig.js` to inject deployed addresses into `frontend/config.js`.

### Install dependencies
- Root:
  npm install
- Frontend:
  cd frontend && npm install
- Contracts (if separate):
  cd contracts && npm install

### Local dev
- Start Hardhat node:
  npx hardhat node
- Deploy to local:
  npx hardhat run scripts/deploy.js --network localhost
- Start frontend dev server (Vite):
  cd frontend
  npm run dev
- Open demo at http://localhost:5173 (or the port Vite shows)

### Deploy contracts
- Configure `.env`
- Example deploy command:
  npx hardhat run scripts/deploy.js --network mumbai
- After deploy:
  - Verify contract on block explorer:
    npx hardhat verify --network mumbai <CONTRACT_ADDRESS> "<constructorArg1>"
  - Inject address into frontend config or use `scripts/publishAddress.js`

---

## Frontend integration

### payments.js API (example)
Provide a stable API for reusability. Example functions:
- init(config) — initialize with config: { provider, contractAddress, tokenAddress, tokenDecimals, pricePerPlay }
- connectWallet() — prompt wallet connection
- getBalance(address) — returns token balance
- approve(amount) — sends ERC20 approval tx
- buyPlays(amount) — call contract buy function
- on(event, handler) — subscribe to contract events (Purchased, Redeemed)
- consumePlay(playerId) — call contract consume function (owner/admin)

Document expected return values and error handling (e.g., insufficient allowance, revert messages).

### UI hooks
- Provide HTML IDs/classes for easy customization:
  - `#connectBtn`, `#balanceDisplay`, `#buyBtn`, `#playsLeft`
- Provide CSS tokens and easy-to-edit variables

---

## Testing

### Contract unit tests
- Located in `contracts/tests`
- Run:
  npx hardhat test
- Coverage:
  - buyPlays happy path
  - buyPlays insufficient allowance / balance
  - consumePlay access controls
  - setPrice owner-only
  - withdraw onlyOwner, reentrancy checks

### Fuzzing / property tests
- Use Foundry/Echidna or Hardhat with property-based testing for edge cases: very large amounts, decimals mismatch, overflow.

### Frontend E2E
- Playwright/Cypress integration:
  - Use a forked mainnet or local Hardhat node seeded with accounts
  - Automate approve -> buy -> start -> consume user flow

---

## CI / CD
Include GitHub Actions:
- lint + format checks
- run solidity tests (Hardhat/Foundry)
- build frontend (Vite/Rollup)
- optionally deploy demo to Vercel/Netlify on push to `main`
- secure secrets in GitHub (PRIVATE_KEY, RPC_URL, ETHERSCAN_API_KEY)

Example workflows are in `/ci`.

---

## Packaging & delivery
- Prepare bundles:
  - UMD bundle for non-bundler users (script tag)
  - ESM package for npm: publish under `@cotopia/forth-connect-ai` if desired
- Create ZIP package with:
  - frontend built assets
  - contracts + ABIs
  - deploy scripts and `.env.example`
  - docs, LICENSE, TERMS, PRIVACY
  - demo screencast (MP4/GIF)
- Delivery options:
  - Gumroad, Paddle, Stripe + direct download link
  - Marketplace like Itch / ThemeForest (follow marketplace rules)
  - Private GitHub repo access for buyers (invite after purchase)
- License keys:
  - Generate per-purchase keys and include in download email
  - Optionally validate license keys via lightweight Cotopia license server (example included in `/scripts/license-server/`)

---

## Security & audits
- Use OpenZeppelin contracts and SafeERC20 wrappers
- Run static analyzers: Slither, MythX
- Fuzz with Echidna or Foundry
- Suggested contract protections:
  - ReentrancyGuard
  - OnlyOwner checks
  - Withdraw timelock (optional)
- Recommend third-party audits for templates that will hold real funds
- Provide responsible disclosure instructions and consider a bug bounty program

---

## Recommended production hardening
- Replace deployer single-owner with Gnosis Safe multisig after deployment
- Use stablecoins (USDC/USDT) for deterministic pricing; document token decimals and behavior
- Enable timelock for critical admin operations (setPrice/withdraw) where transparency is required
- Monitor deployed contracts for unusual patterns (index Purchased/Withdraw events)
- Provide migration scripts for changing token addresses, owners, or contract upgrades (use proxy pattern if supporting upgrades)

---

## Common pitfalls & troubleshooting
- Fee‑on‑transfer tokens: many tokens charge fees; your buy flow must handle them (use SafeERC20 and adjust price logic)
- Token decimals mismatch: ensure price is expressed relative to token decimals
- Gas spikes: users may experience high gas; provide warnings and consider meta‑transactions or gas estimation
- Allowance issues: always request `approve` for the exact required amount or use ERC20 permit (EIP-2612) integration
- Explorer verification: ensure constructor args and metadata are accurate before verify step

---

## Support & professional services
Cotopia offers:
- Basic email support for Pro buyers (30 days) — see TERMS_OF_SALE.md
- Enterprise support & SLA
- Paid services: contract audits, custom integration, white-labeling, analytics hosting

Support email: support@cotopia.org

Include `/support` templates: purchase confirmations, onboarding, and refund request flow.

---

## Changelog
- v1.0 — Oct. 2025
  - Initial release: templates, contracts, payments.js, docs, CI, demo

---

## Contact & legal
Prepared by Cotopia (Wyoming, USA) • Support: support@cotopia.org  
Version 1.0 — Last updated: Oct. 2025

This README and the included legal templates are provided for informational purposes and are not a substitute for legal advice. Consult qualified counsel for jurisdiction-specific guidance.

---
