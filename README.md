⚡ XENOS Perpetual Engine (TypeScript Mock)

“Trade the Chaos — locally, deterministically, and without custody.”

🧠 Overview

This repository contains a fully self-contained mock implementation of the XENOS perpetual trading engine, inspired by the Solana-based design of the real protocol.

It simulates the core logic of a perp DEX + meme futures platform, including:

Perpetual markets (PUMP-USD, WIF-USD, etc.)

Lending and margin accounts

Cross/isolated margin models

Order book and matching engine

Funding rate computation

Liquidation and risk management

Fee routing & Streamflow-style token locks

Treasury and buyback tracking

Advanced order types and algos (TWAP, Iceberg, Stop-Limit, OCO)

Maker bot, orchestrator, latency model, and basic backtester

⚠️ This is demo code only — there is no blockchain, no RPC, no custody, and no real money.
Everything runs in-memory with deterministic pseudo-random behavior.

🧩 Architecture Overview

The mock engine is modular, simulating components found in a real Solana perp DEX:

Module	Description
Oracle	Pyth + Switchboard–style price feeds with noise, drift, and timestamps
OrderBook	Classic price-time priority matching engine
FundingEngine	EMA-based funding rate model (up to ±0.25% per period)
RiskEngine	IMR/MMR margin checks, partial liquidation logic
FeeRouter	Calculates taker/maker/creator/protocol fees
Treasury	Splits creator fees into buyback, marketing, ops, and rewards
StreamflowLock	Simulated permanent token lock mechanism
Market	Encapsulates trading, settlement, and funding updates
Exchange	Manages all markets and accounts globally
AdvancedOrderBook	Handles Stop, Stop-Limit, Trailing, OCO, Iceberg logic
ExecutionAlgo	TWAP/VWAP order slicer
MakerBot	Passive quoting bot with inventory skew control
Backtester	Simple strategy tester with PnL, drawdown, and Sharpe ratio
Orchestrator	Multi-market controller + latency simulation
Serializer	Saves snapshot of system state (accounts, markets, treasury)
⚙️ Setup & Run
1. Clone & Install
git clone https://github.com/4nt30n1/xenos-prototype/tree/main
cd xenos-perp-engine
npm install typescript ts-node

2. Run the Demo
npx ts-node xenos_mock_perp_engine.ts


You’ll see console output showing:

Market creation

Order matching & fills

Alice & Bob positions

Funding rate updates

Treasury buyback report

Optional backtest metrics

Example:

[PUMP-USD] OB  bestBid=0.00369 bestAsk=0.00372 mark=0.003705
Alice taker buy filled: 10000
[BACKTEST] { pnl: 82.1345, maxDD: 0.071, trades: 88, sharpe: 1.47 }

🧱 Key Design Features
💹 Order Matching

Full price-time priority queue

GTC, IOC, FOK, post-only, reduce-only support

Advanced types: Stop, Stop-Limit, Trailing Stop, OCO, Iceberg

⚡ Funding & Risk

EMA-based funding rate derived from oracle vs. mark spread

Initial margin: 10%, maintenance margin: 6%

Partial liquidation mechanism (with 1.5% penalty)

💰 Fee & Treasury Flow

Each trade triggers creator’s fee redistribution:

50% → Streamflow lock
10% → Treasury (buyback)
25% → Marketing
7.5% → Team Ops
7.5% → Trader Rewards


Simulated in-memory with persistent accounting.

🧮 Backtester

Evaluate simple trading logic:

const res = bt.run(ex, mkt, (t, mark, acc, mkt) => {
  if (Math.sin(t / 10) > 0) {
    mkt.submit(acc, makeOrder({ side: 'buy', size: 2000, price: mark * 1.001, ... }));
  } else {
    mkt.submit(acc, makeOrder({ side: 'sell', size: 2000, price: mark * 0.999, ... }));
  }
}, 180);


Returns { pnl, maxDD, trades, sharpe }.

🧠 Advanced Extensions
1. Execution Algos

TWAP/VWAP slicing for large orders.

2. MakerBot

Market maker quotes bid/ask around mid-price with configurable spread and inventory skew.

3. Latency Simulation

Simulated message delays with random jitter for more realistic order sequencing.

4. Serialization

Save current exchange state to JSON snapshot.

🧪 Quick API Reference
// Core types
import { Exchange, Market, makeOrder, TimeInForce, Account } from './xenos_mock_perp_engine'

// Create exchange and market
const ex = new Exchange({ 'PUMP-USD': 0.0037 })
const mkt = ex.createMarket('PUMP-USD')

// Create account and deposit funds
const alice = ex.getAccount('alice')
alice.deposit('USD', 1000)

// Place order
ex.submitOrder(makeOrder({
  accountId: 'alice',
  market: 'PUMP-USD',
  side: 'buy',
  price: 0.00375,
  size: 5000,
  tif: TimeInForce.IOC
}))

📊 Treasury Accounting Example

After running the demo:

--- Treasury Report ---
{
  streamflowLocked: 123.4578,
  xenosTreasury: 24.6916,
  marketing: 61.7291,
  ops: 18.5187,
  traderRewards: 18.5187
}


All amounts are simulated in “USD-equivalent”.

🧩 Architecture Diagram (conceptual)
[ Exchange ]
   ├── [ Market: PUMP-USD ]
   │     ├── OrderBook
   │     ├── FundingEngine
   │     ├── RiskEngine
   │     └── FeeRouter → Treasury → Streamflow
   ├── [ Market: WIF-USD ]
   ├── [ Oracle ]
   └── [ Accounts / Positions ]

🧰 Tools & Stack

Language: TypeScript (strict mode)

Runtime: Node.js 18+

Dependencies: none (pure TS)

Style: 100% deterministic, no async

Line count: ~1,200 (core + extensions)

🔒 Security Notice

This code is not production-ready, not audited, and not intended for deployment on mainnet.
It’s purely for educational and prototype use.

🧬 License

MIT License — feel free to fork, modify, and experiment.
Attribution appreciated: © 2025 XENOS Labs

✨ Author

XENOS Labs — Perp DEX + Meme Futures on Solana

Built to survive the chaos — not to avoid it.
