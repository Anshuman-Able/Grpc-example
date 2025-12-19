# 🚀 Yellowstone gRPC - Multi-Address Subscription Issue Demo

This repository demonstrates a **data loss/backpressure issue** when subscribing to multiple addresses using Yellowstone gRPC.

## ⚠️ The Problem

When using Yellowstone gRPC's `SubscribeRequest` with **multiple addresses** in the `accountInclude` filter, transactions start getting **dropped or missed** — likely due to high throughput or backpressure handling issues.

### ✅ Works Fine (Single Address)
```typescript
accountInclude: [PUMPFUN_MINT_AUTHORITY.toBase58()]
```

### ❌ Data Loss Occurs (Multiple Addresses)
```typescript
accountInclude: [PUMPFUN_MINT_AUTHORITY.toBase58(), TOKEN_PROGRAM_ID.toBase58()]
```

When adding more than one monitoring address, the stream experiences:
- **Missing transactions** - Not all transactions are received
- **Data gaps** - Intermittent data loss during high activity periods
- **Potential backpressure** - The client may not be consuming data fast enough

## Overview

This TypeScript application streams and decodes PumpFun token creation transactions in real-time from the Solana blockchain using Yellowstone gRPC. It serves as a minimal reproduction case for the multi-address subscription issue.

## Features

- ⚡ **Real-time monitoring** - Streams live transactions using Yellowstone gRPC
- 🔍 **Transaction decoding** - Full parsing of PumpFun create instructions and events
- 📊 **Detailed token info** - Extracts name, symbol, URI, mint address, bonding curve, reserves, and supply
- 🔄 **Auto-reconnect** - Automatically reconnects on stream errors
- 🛡️ **Error handling** - Robust error handling and logging

## Prerequisites

- Node.js (v18+)
- npm or yarn
- A Yellowstone gRPC endpoint (e.g., from [Shyft](https://shyft.to/) or [Triton](https://triton.one/))

## Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Anshuman-Able/Grpc-example.git
   cd Grpc-example
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**
   
   Create a `.env` file in the root directory:
   ```env
   GRPC_URL=<your-yellowstone-grpc-endpoint>
   X_TOKEN=<your-api-token>
   ```

   | Variable | Description |
   |----------|-------------|
   | `GRPC_URL` | Your Yellowstone gRPC endpoint URL |
   | `X_TOKEN` | Your authentication token for the gRPC service |

## Usage

**Start the stream:**
```bash
npm start
```

The application will connect to the gRPC endpoint and begin streaming newly minted PumpFun tokens:

```
Streaming Newly Minted Tokens on Pumpfun...
```

### Sample Output

```json
{
  "Name": "Sample Token",
  "Symbol": "SMPL",
  "Uri": "https://ipfs.io/ipfs/...",
  "Mint": "ABC123...xyz",
  "Bonding_Curve": "DEF456...abc",
  "Creator": "GHI789...def",
  "VirtualTokenReserves": "1000000000",
  "VirtualSolReserves": "30000000",
  "RealTokenReserves": "800000000",
  "TokenTotalSupply": "1000000000"
}
```

## Project Structure

```
├── index.ts                    # Main entry point - stream handler
├── utils/
│   ├── type.ts                 # Constants & parser configurations
│   ├── decode-parser.ts        # PumpFun transaction decoder
│   ├── pumpfun_formatted_txn.ts # Token data formatter
│   ├── transaction-formatter.ts # Raw transaction formatter
│   ├── event-parser.ts         # Event parsing utilities
│   └── bn-layout-formatter.ts  # BigNumber formatting
├── idls/
│   └── pump_0.1.0.json         # PumpFun program IDL
├── package.json
├── tsconfig.json
└── .env                        # Environment configuration (create this)
```

## Key Components

| File | Description |
|------|-------------|
| `index.ts` | Initializes gRPC client and handles the subscription stream |
| `decode-parser.ts` | Decodes PumpFun instructions using the program IDL |
| `pumpfun_formatted_txn.ts` | Extracts and formats token creation data |
| `type.ts` | Defines program IDs, constants, and parser instances |

## Dependencies

| Package | Purpose |
|---------|---------|
| `@triton-one/yellowstone-grpc` | Yellowstone gRPC client for Solana |
| `@solana/web3.js` | Solana Web3 library |
| `@coral-xyz/anchor` | Anchor framework for IDL parsing |
| `@shyft-to/solana-transaction-parser` | Transaction parsing utilities |
| `dotenv` | Environment variable management |

## Scripts

| Script | Command | Description |
|--------|---------|-------------|
| `start` | `npm start` | Run the streaming service |
| `build` | `npm run build` | Compile TypeScript to JavaScript |

## Getting gRPC Access

You can obtain Yellowstone gRPC endpoints from:

- **[Shyft](https://shyft.to/)** - Provides gRPC access with free tier available
- **[Triton](https://triton.one/)** - Official Yellowstone gRPC provider
- **[Helius](https://helius.dev/)** - Solana infrastructure provider

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Connection timeout | Verify your `GRPC_URL` is correct and accessible |
| Authentication error | Check your `X_TOKEN` is valid |
| No output | Ensure PumpFun tokens are being created (check in low activity periods) |
| Parser warnings | These are suppressed by default; the app handles unrecognized instructions gracefully |
