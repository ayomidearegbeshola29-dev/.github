# NexDAO

> **Modular, open-source DAO framework on Soroban** — on-chain voting, treasury management, and token-gated governance for real organisations on Stellar.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Stellar](https://img.shields.io/badge/Stellar-Soroban-000000?logo=stellar)](https://stellar.org)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![GitHub Org](https://img.shields.io/badge/GitHub-ayomidearegbeshola29--dev-181717?logo=github)](https://github.com/ayomidearegbeshola29-dev)

---

## Table of Contents

- [About NexDAO](#about-nexdao)
- [Architecture Overview](#architecture-overview)
- [Repository Structure](#repository-structure)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Development Roadmap](#development-roadmap)
- [Contributing](#contributing)
- [Community](#community)
- [License](#license)

---

## About NexDAO

NexDAO is a complete, modular DAO framework built on Stellar's Soroban smart contracts platform. It provides everything you need to launch and operate a decentralised autonomous organisation:

- **On-chain governance** — Proposal creation, quadratic voting, and time-lock execution
- **Treasury management** — Multi-sig treasury with spending limits and quorum-based execution
- **Token-gated voting** — SEP-41 governance token determines voting weight
- **Real-time dashboard** — Live proposal feed, vote tracking, and treasury overview
- **Developer-friendly** — Comprehensive API, SDK, and documentation

### Why NexDAO?

| Feature | NexDAO | Other DAO Frameworks |
|---------|--------|---------------------|
| **Blockchain** | Stellar (Soroban) | Ethereum/EVM |
| **Gas fees** | Near-zero | High |
| **Speed** | 3-5 second finality | Minutes |
| **Voting** | Quadratic | Simple majority |
| **Treasury** | Multi-sig native | Plugin-based |
| **Language** | Rust | Solidity |
| **Tooling** | Stellar SDK + Freighter | MetaMask + Hardhat |

---

## Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          NEXDAO ECOSYSTEM                                    │
│                                                                              │
│  ┌─────────────────┐     ┌─────────────────┐     ┌──────────────────────┐   │
│  │                   │     │                   │     │                        │   │
│  │   nexdao-frontend │────▶│   nexdao-backend  │────▶│   Stellar Soroban    │   │
│  │   (Next.js UI)   │     │   (Express API)   │     │   (Smart Contracts)  │   │
│  │                   │◀────│                   │◀────│                        │   │
│  └─────────────────┘     └─────────────────┘     └──────────────────────┘   │
│         │                       │                           │                │
│         │                       │                           │                │
│         ▼                       ▼                           ▼                │
│  ┌─────────────────┐     ┌─────────────────┐     ┌──────────────────────┐   │
│  │   Freighter      │     │   PostgreSQL    │     │   Governance        │   │
│  │   Wallet         │     │   Database      │     │   + Treasury + Token│   │
│  └─────────────────┘     └─────────────────┘     └──────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Component Interaction Flow

```
                          VOTING FLOW
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│          │     │          │     │          │     │          │
│  User    │────▶│ Frontend │────▶│ Backend  │────▶│ Soroban  │
│ (Wallet) │     │ (UI)     │     │ (API)    │     │ Contract │
│          │◀────│          │◀────│          │◀────│          │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
     │                                                  │
     │           1. Create Proposal                     │
     │─────────────────────────────────────────────────►│
     │                                                  │
     │           2. Vote on Proposal                    │
     │─────────────────────────────────────────────────►│
     │                                                  │
     │           3. Execute Proposal                    │
     │─────────────────────────────────────────────────►│
     │                                                  │
     │                     4. Treasury Transfer         │
     │─────────────────────────────────────────────────►│
```

### Smart Contract Architecture

```
┌─────────────────────────────────────────────────┐
│              CONTRACT INTERACTIONS               │
│                                                   │
│  ┌─────────────────────────────────────────┐    │
│  │         governance.create_proposal()      │    │
│  │         governance.vote()                 │    │
│  │         governance.execute()              │    │
│  └─────────────────────────────────────────┘    │
│                        │                        │
│                        ▼                        │
│  ┌─────────────────────────────────────────┐    │
│  │         treasury.execute_spend()         │    │
│  │                    │                      │    │
│  │                    ▼                      │    │
│  │  ┌─────────────────────────────────┐    │    │
│  │  │  token.transfer() (USDC/XLM)    │    │    │
│  │  └─────────────────────────────────┘    │    │
│  └─────────────────────────────────────────┘    │
│                                                   │
│  token.balance() ◀── governance contract           │
│  (for quadratic weight calculation)               │
└─────────────────────────────────────────────────┘
```

### Data Flow Sequence

```
Frontend                    Backend                 Soroban RPC              Stellar
   │                          │                        │                      │
   │  1. GET /api/proposals   │                        │                      │
   │─────────────────────────►│                        │                      │
   │                          │                        │                      │
   │  2. Return proposals     │                        │                      │
   │◄─────────────────────────│                        │                      │
   │                          │                        │                      │
   │  3. Show proposal UI     │                        │                      │
   │    (User signs tx in     │                        │                      │
   │     Freighter wallet)    │                        │                      │
   │                          │                        │                      │
   │  4. POST /vote {signedXdr}                        │                      │
   │─────────────────────────►│                        │                      │
   │                          │  5. submitTransaction  │                      │
   │                          │───────────────────────►│                      │
   │                          │                        │  6. Consensus        │
   │                          │                        │─────────────────────►│
   │                          │                        │◄─────────────────────│
   │                          │◄───────────────────────│                      │
   │◄─────────────────────────│                        │                      │
   │                          │                        │                      │
   │  7. SSE: VoteCastEvent   │                        │                      │
   │◄─────────────────────────│                        │                      │
   │                          │                        │                      │
```

---

## Repository Structure

The NexDAO ecosystem is split across multiple repositories:

| Repository | Description | Stack | Status |
|------------|-------------|-------|--------|
| [nexdao](https://github.com/ayomidearegbeshola29-dev/nexdao) | Organisation overview & monorepo | — | ✅ |
| [nexdao-frontend](https://github.com/ayomidearegbeshola29-dev/nexdao-frontend) | Governance dashboard | Next.js, TypeScript, Tailwind CSS, Radix UI | ✅ |
| [nexdao-backend](https://github.com/ayomidearegbeshola29-dev/nexdao-backend) | REST API + Soroban integration | Node.js, Express, Prisma, PostgreSQL | ✅ |
| [nexdao-contracts](https://github.com/ayomidearegbeshola29-dev/nexdao-contracts) | Soroban smart contracts | Rust, Soroban SDK | 🚧 |
| [nexdao-docs](https://github.com/ayomidearegbeshola29-dev/nexdao-docs) | Protocol documentation | Astro, Starlight | 🚧 |
| [.github](https://github.com/ayomidearegbeshola29-dev/.github) | Community health files | — | ✅ |

### Dependency Graph

```
                  ┌──────────────────┐
                  │   nexdao-docs    │
                  │  (Documentation) │
                  └────────┬─────────┘
                           │ references
                           ▼
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  nexdao-frontend │────▶│  nexdao-backend  │────▶│nexdao-contracts  │
│  (Dashboard UI)  │     │  (REST API)      │     │(Soroban Contracts)│
└──────────────────┘     └──────────────────┘     └──────────────────┘
         │                       │                        │
         │                       │                        │
         ▼                       ▼                        ▼
    Freighter               PostgreSQL                Stellar
    Wallet                  Database                  Network
```

---

## Tech Stack

### Frontend

| Technology | Purpose |
|------------|---------|
| [Next.js 14](https://nextjs.org/) | React framework with App Router |
| [TypeScript](https://www.typescriptlang.org/) | Type-safe JavaScript |
| [Tailwind CSS](https://tailwindcss.com/) | Utility-first styling |
| [Radix UI](https://www.radix-ui.com/) | Accessible UI primitives |
| [TanStack Query](https://tanstack.com/query) | Server state management |
| [Lucide React](https://lucide.dev/) | Icon library |
| [@stellar/stellar-sdk](https://github.com/stellar/stellar-sdk) | Stellar blockchain SDK |

### Backend

| Technology | Purpose |
|------------|---------|
| [Node.js 20](https://nodejs.org/) | JavaScript runtime |
| [Express 4](https://expressjs.com/) | Web framework |
| [TypeScript](https://www.typescriptlang.org/) | Type-safe JavaScript |
| [Prisma 5](https://www.prisma.io/) | Database ORM |
| [PostgreSQL 16](https://www.postgresql.org/) | Relational database |
| [Zod 3](https://zod.dev/) | Schema validation |
| [@stellar/stellar-sdk](https://github.com/stellar/stellar-sdk) | Stellar blockchain SDK |

### Smart Contracts

| Technology | Purpose |
|------------|---------|
| [Rust](https://www.rust-lang.org/) | Smart contract language |
| [Soroban SDK](https://soroban.stellar.org/) | Stellar smart contract platform |
| [SEP-41](https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0041.md) | Token standard |

---

## Getting Started

### Quick Start

```bash
# 1. Clone all repositories
git clone https://github.com/ayomidearegbeshola29-dev/nexdao-frontend.git
git clone https://github.com/ayomidearegbeshola29-dev/nexdao-backend.git

# 2. Start the backend
cd nexdao-backend
npm install
cp .env.example .env
npx prisma generate && npx prisma db push
npm run dev

# 3. Start the frontend (in a new terminal)
cd ../nexdao-frontend
npm install
cp .env.example .env.local
npm run dev
```

### Prerequisites

- **Node.js** 20+ and npm
- **PostgreSQL** 16+ (or Docker for local development)
- **Freighter Wallet** browser extension (Stellar wallet)
- **Rust** (for smart contract development)

---

## How It Works

### 1. Proposal Lifecycle

```
Created ──► Active ──► Passed/Rejected ──► Executed
   │           │             │                  │
   │           ▼             │                  │
   │      Voting Open        │                  │
   │    Quadratic Weights    │                  │
   │    Quorum Required      │                  │
   │                         │                  │
   │                  Deadline Reached          │
   │                  Votes Tallied             │
   │                                            │
   │                                     Treasury
   │                                     Transfer
```

### 2. Quadratic Voting

Voting power is calculated quadratically based on token balance:

```
votingWeight = sqrt(numberOfTokens)
```

This ensures that larger token holders have proportionally less influence, promoting more democratic governance.

### 3. Treasury Multi-Sig

Treasury spend proposals require:
1. **Proposal creation** — Any member can propose a spend
2. **Voting period** — Members vote on the proposal
3. **Quorum achieved** — Minimum participation threshold met
4. **Majority approval** — More for than against
5. **Execution** — Multi-sig signers execute the transfer

---

## Development Roadmap

### Phase 1: Core Infrastructure ✅
- [x] Project structure and monorepo setup
- [x] Frontend dashboard with Next.js
- [x] Backend API with Express + Prisma
- [x] Database schema and migrations

### Phase 2: Smart Contracts 🚧
- [ ] Governance contract (proposal + quadratic voting)
- [ ] Treasury multi-sig contract
- [ ] SEP-41 governance token
- [ ] Contract testing suite

### Phase 3: Integration 🚧
- [ ] Frontend ↔ Backend API integration
- [ ] Backend ↔ Soroban RPC integration
- [ ] Freighter wallet connection
- [ ] Transaction signing flow

### Phase 4: Real-Time Features 📋
- [ ] Webhook listener for on-chain events
- [ ] SSE push to frontend
- [ ] Real-time vote updates
- [ ] Live quorum tracking

### Phase 5: Production Readiness 📋
- [ ] Comprehensive test suite
- [ ] Security audit
- [ ] Docker deployment
- [ ] CI/CD pipeline

---

## Contributing

We welcome contributions from developers of all skill levels! Whether you're fixing a bug, adding a feature, or improving documentation, your help is appreciated.

### How to Contribute

1. **Browse issues** — Check our [issue tracker](https://github.com/ayomidearegbeshola29-dev/nexdao/issues) for tasks tagged `good-first-issue` or `help-wanted`
2. **Fork the repo** — Create your own fork of the relevant repository
3. **Create a branch** — `git checkout -b feature/amazing-feature`
4. **Commit changes** — `git commit -m 'Add some amazing feature'`
5. **Push to branch** — `git push origin feature/amazing-feature`
6. **Open a PR** — Submit a pull request with a clear description

### Development Guidelines

- Follow the existing code style and conventions
- Write tests for new features
- Update documentation as needed
- Keep PRs focused and manageable in size

For detailed guidelines, see [CONTRIBUTING.md](CONTRIBUTING.md).

---

## Community

- **Issues** — Bug reports and feature requests
- **Discussions** — Questions and ideas (coming soon)
- **Security** — Report vulnerabilities to [SECURITY.md](SECURITY.md)

### Good First Issues

Ready to contribute? Start with these:

| Repository | Issue | Difficulty |
|------------|-------|------------|
| nexdao-frontend | Add wallet connect button | Easy |
| nexdao-frontend | Implement proposal creation form | Medium |
| nexdao-backend | Add API endpoint tests | Easy |
| nexdao-backend | Implement SSE push for votes | Medium |
| nexdao-contracts | Write contract unit tests | Medium |

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2025 NexDAO

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## Image Assets

> 🎨 The following diagrams are available as standalone images in the `assets/` directory:

| Diagram | Description |
|---------|-------------|
| `architecture-overview.png` | High-level system architecture |
| `voting-flow.png` | End-to-end voting flow |
| `contract-interactions.png` | Smart contract interaction diagram |
| `data-flow-sequence.png` | Data flow sequence diagram |
| `proposal-lifecycle.png` | Proposal lifecycle state machine |
| `repo-dependencies.png` | Repository dependency graph |

<!-- 
  To generate these diagrams:
  1. Use a tool like Mermaid, Draw.io, or Excalidraw
  2. Export to PNG/SVG and place in the assets/ directory
  3. Reference them in markdown like: ![Architecture](assets/architecture-overview.png)
-->

---

<p align="center">
  Built with ❤️ for the Stellar ecosystem
</p>
<p align="center">
  <a href="https://github.com/ayomidearegbeshola29-dev">ayomidearegbeshola29-dev</a>
</p>
