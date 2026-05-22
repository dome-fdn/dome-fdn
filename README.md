<div align="center">

```
██████╗  ██████╗ ███╗   ███╗███████╗
██╔══██╗██╔═══██╗████╗ ████║██╔════╝
██║  ██║██║   ██║██╔████╔██║█████╗  
██║  ██║██║   ██║██║╚██╔╝██║██╔══╝  
██████╔╝╚██████╔╝██║ ╚═╝ ██║███████╗
╚═════╝  ╚═════╝ ╚═╝     ╚═╝╚══════╝
```

### Private ETH & USDC on EVM — powered by Groth16 zero-knowledge proofs

[![Base](https://img.shields.io/badge/Base-0052FF?style=for-the-badge&logo=coinbase&logoColor=white)](https://base.org)
[![Solidity](https://img.shields.io/badge/Solidity-363636?style=for-the-badge&logo=solidity&logoColor=white)](https://soliditylang.org)
[![Circom](https://img.shields.io/badge/Circom-000000?style=for-the-badge&logo=circom&logoColor=white)](https://docs.circom.io)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org)
[![Expo](https://img.shields.io/badge/Expo-000020?style=for-the-badge&logo=expo&logoColor=white)](https://expo.dev)
[![Hardhat](https://img.shields.io/badge/Hardhat-FFF100?style=for-the-badge&logo=ethereum&logoColor=black)](https://hardhat.org)
[![Groth16](https://img.shields.io/badge/Proving-Groth16-6366F1?style=for-the-badge)](https://docs.circom.io)

</div>

---

## What is Dome?

**Dome** is a shielded transaction layer for EVM chains. Users deposit native ETH or USDC into on-chain **shielded pools**, receive private notes backed by a Merkle tree of commitments, and later withdraw to any address — without linking deposit and withdrawal on-chain.

The protocol combines:

- **Solidity smart contracts** — shielded pools, nullifiers, Merkle tree, Groth16 verifier
- **Circom circuits** — `transaction2` spend proofs (Merkle inclusion, nullifier derivation, multi-output)
- **TypeScript SDK** — deposit, withdraw, balance, session sign-in for wallets
- **Backend services** — indexer (UTXO scan + Merkle paths), relayer, JSON-RPC proxy
- **Client apps** — Next.js web wallet and Expo mobile shell

Target chain: **[Base](https://base.org)** (Sepolia for development).

```
  User wallet                Dome backend              Base L2
 ┌─────────────┐            ┌──────────────┐         ┌─────────────┐
 │  Sign in    │─── prove ─▶│   Indexer    │◀─ logs ─│ Shielded    │
 │  Deposit    │            │   Relayer    │── tx ──▶│ ETH / USDC  │
 │  Withdraw   │◀─ path ────│   RPC proxy  │         │ pools       │
 └─────────────┘            └──────────────┘         └─────────────┘
        │                                                    │
        └──────────── Groth16 proof (Circom / snarkjs) ──────┘
```

Session authentication uses a standard wallet signature:

```
DOME_SIGN_IN_MESSAGE = "Dome shielded account sign in"
```

---

## Repositories

| Repository | Description | Status |
| --- | --- | --- |
| [**dome-core-evm**](https://github.com/dome-fdn/dome-core-evm) | Hardhat contracts, Circom circuits, deploy scripts, Groth16 artifacts | Active |
| [**dome-sdk-evm**](https://github.com/dome-fdn/dome-sdk-evm) | TypeScript SDK — `@dome/sdk-evm` for deposit / withdraw / balance | Active |
| [**dome-backend**](https://github.com/dome-fdn/dome-backend) | Indexer, relayer, RPC proxy, dev faucet | Active |
| [**dome-web**](https://github.com/dome-fdn/dome-web) | Next.js web wallet for shielded flows on Base | Active |
| [**dome-mobile**](https://github.com/dome-fdn/dome-mobile) | Expo / React Native wallet shell | UX shell — SDK integration Phase 2 |
| [**dome-contracts**](https://github.com/dome-fdn/dome-contracts) | Legacy Foundry shielded pool (pre–core-evm) | Archived |
| [**dome-circuits**](https://github.com/dome-fdn/dome-circuits) | Legacy Circom spend circuit | Archived |

---

## Stack at a glance

| Layer | Technology |
| --- | --- |
| **Chain** | Base / Base Sepolia |
| **Contracts** | Solidity, Hardhat, OpenZeppelin, Poseidon hash |
| **ZK** | Circom 2, snarkjs, Groth16 (`transaction2.circom`) |
| **SDK** | TypeScript, ethers.js |
| **Backend** | Node.js, SQLite / Postgres indexer |
| **Web** | Next.js, EIP-1193 wallets |
| **Mobile** | Expo, React Native, Expo Router |

---

## Getting started

1. **Contracts & circuits** — clone [`dome-core-evm`](https://github.com/dome-fdn/dome-core-evm), run `npm install && npm run compile && npm run deploy:testnet`
2. **Backend** — clone [`dome-backend`](https://github.com/dome-fdn/dome-backend), configure `.env`, start indexer + relayer
3. **Web wallet** — clone [`dome-web`](https://github.com/dome-fdn/dome-web), point env vars at deployed pools and backend
4. **Integrate** — install [`@dome/sdk-evm`](https://github.com/dome-fdn/dome-sdk-evm) or use it from source

Circuit proving keys (`transaction2.wasm`, `transaction2.zkey`) are served over HTTPS — see [`dome-core-evm`](https://github.com/dome-fdn/dome-core-evm) and [`dome-web/public/circuits/`](https://github.com/dome-fdn/dome-web/tree/main/public/circuits).

---

## Architecture

```mermaid
flowchart LR
  subgraph Clients
    W[dome-web]
    M[dome-mobile]
    S[dome-sdk-evm]
  end

  subgraph Services
    B[dome-backend]
    I[Indexer]
    R[Relayer]
    B --- I
    B --- R
  end

  subgraph On-chain
    P[Shielded ETH Pool]
    U[Shielded USDC Pool]
    V[Groth16 Verifier]
    P --- V
    U --- V
  end

  W --> S
  M -.-> S
  S --> B
  S --> P
  R --> P
  I --> P
```

---

## Status

| Milestone | State |
| --- | --- |
| Shielded ETH deposit / withdraw (web) | Implemented |
| Indexer + relayer | Implemented |
| Base Sepolia testnet deploy | Supported |
| Mobile shielded flows | Planned (Phase 2) |
| Mainnet | Pre-audit — not production-ready |

---

<div align="center">

**[dome-fdn](https://github.com/dome-fdn)** · Open source shielded EVM infrastructure

</div>
