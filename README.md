# 🚀 Binance Autotrade – Order Block Based Futures Trading System

A fully automated Binance Futures trading system built with n8n, implementing custom Order Block detection, structured risk management, and dynamic Stop Loss / Take Profit logic using Binance API (HMAC SHA256 signed requests).

---

## 📌 Overview

This project is an end-to-end automated trading workflow that:

* Detects Bullish & Bearish Order Blocks
* Executes LIMIT entries
* Places Algo-based Stop Loss & Take Profit
* Dynamically moves Stop Loss (Breakeven & Profit Lock)
* Manages leverage automatically
* Prevents duplicate or conflicting orders
* Continuously monitors open positions

Built using:

* 🧠 Custom market structure logic (Order Block + ABC structure)
* 🔁 n8n automation workflows
* 🔐 Binance Futures REST API (signed)
* 📊 1H timeframe market structure detection

---

# 🧠 Strategy Logic

## 1️⃣ Order Block Detection

The system analyzes OHLCV data and identifies:

* Volume Pivot High (PHV)
* Market Structure Break (Bullish / Bearish)
* Valid Order Blocks
* Mitigated Order Blocks (automatically removed)

Bullish OB:

* Entry at OB average
* Stop Loss at OB bottom

Bearish OB:

* Entry at OB average
* Stop Loss at OB top

---

## 2️⃣ ABC Market Structure & Take Profit Logic

After detecting an Order Block, the system calculates:

* A → Major Swing
* B → Impulse
* C → Retracement

Retracement ratio:

Retracement = BC / AB

Classification:

* Retracement ≤ 0.382 → Middle continuation
* Retracement > 0.382 → Early structure

Take Profit projection:

* 1.0 Fibonacci
* 0.786 Fibonacci (conditional)

---

## 3️⃣ Dynamic Stop Loss Movement

Stop Loss automatically adjusts based on unrealized profit:

| Profit % | Action                       |
| -------- | ---------------------------- |
| ≥ 2%     | Move SL to Entry (Breakeven) |
| ≥ 4%     | Lock 2% profit               |

Profit calculation:

Percent Change = (Current Price - Entry Price) / Entry Price × 100%

System includes anti-downgrade logic (SL never moves backward).

---

# ⚙️ System Architecture

### 🔹 Core Modules

1. Market Data Fetch

   * 1H OHLC from Binance Futures
   * Volume + structure analysis

2. Order Block Engine

   * Detects OB
   * Filters mitigated blocks
   * Returns active OB list

3. Position Monitor

   * Checks open positions
   * Identifies side (BUY / SELL)
   * Retrieves unrealized PnL

4. Risk Engine

   * Calculates TP via ABC structure
   * Adjusts SL dynamically
   * Ensures proper RR ratio

5. Execution Layer

   * LIMIT entry orders
   * STOP_MARKET via `/fapi/v1/algoOrder`
   * TAKE_PROFIT_MARKET via Algo Orders
   * Leverage control

6. Order Cleanup

   * Detects duplicate limit orders
   * Deletes outdated SL/TP
   * Maintains clean order book

---

# 🔐 Security

* All private endpoints signed using:

  * HMAC SHA256
* Uses:

  * `timestamp`
  * `recvWindow`
* API Keys stored securely (Google Sheets / environment)

---

# 📂 Workflow Built With

* n8n (Automation engine)
* HTTP Request nodes
* Crypto node (HMAC SHA256)
* Code nodes (JavaScript logic)
* Google Sheets (account storage)

---

# 📊 Features

✅ Order Block detection
✅ Structure-based Take Profit
✅ Dynamic Stop Loss
✅ AlgoOrder management
✅ Leverage control
✅ Position detection
✅ Anti duplicate limit order logic
✅ Automatic SL upgrade logic
✅ Risk-controlled execution

---

# 🧪 Example Trade Flow

1. Detect Bullish OB
2. Place LIMIT BUY at OB average
3. Set STOP_MARKET below OB
4. Calculate ABC structure
5. Place TAKE_PROFIT_MARKET
6. If price moves +2% → SL moves to breakeven
7. If +4% → SL locks profit

Fully automated.

---

# ⚠️ Risk Disclaimer

This project is for educational and research purposes only.

Trading cryptocurrency futures involves high risk.
Use at your own responsibility.

---

# 📈 Future Improvements

* Multi-timeframe confirmation
* Volatility filter
* Funding rate filter
* Position sizing automation
* Dashboard UI (Next.js / React)
* Telegram notification integration
* Backtesting module

---

# 👨‍💻 Author

Built by Deri Elf
Automation & Trading System Developer
Specialized in workflow automation and API-based system engineering.

---

Jika kamu mau, aku bisa lanjutkan dengan:

* 📊 Tambah architecture diagram visual
* 🧠 Tambah section "Why AlgoOrder instead of normal order"
* 🔥 Buat versi yang lebih “Quant Fund” style
* 🛡 Tambah professional risk section seperti hedge fund documentation
* 📦 Tambahkan folder structure suggestion untuk repo

Tinggal bilang mau upgrade ke level mana 😄
