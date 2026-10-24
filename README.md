# Polymarket BTC 15m Assistant [4h Master Switch]
### Stage 9: Drawdown Filter 1 — 4h 0.5% Master Switch

A real-time, high-performance automated trading bot for Polymarket **"Bitcoin Up or Down" 15-minute** binary options markets, executing via **Good 'Til Date (GTD)** limit orders powered by **Stage 9: Drawdown Filter 1** from the 7,678-market optimization report.

---

## 🏆 Empirical Backtest Reference (Lowest Drawdown of Top Earners)

Evaluated across **7,678 historical Polymarket BTC 15-minute markets**:
- **Rank**: **#4 Overall** (out of 30 simulated strategy configurations)
- **Win Rate**: **70.9%** (480 Wins / 197 Losses across 677 executed trades)
- **Profit Factor**: **1.84**
- **Max Drawdown**: **-42.1%** (Lowest drawdown among all top-tier strategies, slashing drawdown significantly vs the baseline)
- **Annualized Sharpe Ratio**: **5.47**
- **Directional Symmetry**:
  - **UP Trades**: 344 trades | **68.0% Win Rate**
  - **DOWN Trades**: 333 trades | **73.9% Win Rate**

---

## 🧠 Hardcoded Quantitative Strategy Architecture

### 1. Alpha & Entry Rules (`decide_open_entry`)
- **Immediate Open Timing Window ($0 \le t \le 45\text{s}$)**: Executes within the first 45 seconds of market open.
- **15m Structural Heikin-Ashi Edge**:
  - Enters **UP** on 15m HA flat-bottom candles (bullish expansion).
  - Enters **DOWN** on 15m HA flat-top candles (bearish expansion).
- **Cheap Odds Optimization ($0.40 \le \text{ask} \le 0.58$)**: Restricts entries strictly to contract prices between 40¢ and 58¢.
- **Anti-Exhaustion Gate ($< 12.0\text{ bp}$)**: Bypasses markets where the prior completed 15m candle body exceeded $12.0\text{ bp}$.

### 2. The 4h 0.5% Master Switch Gate (`entry_block_reason`)
- **Condition**:
  $$\text{Move}_{4\text{h}} = \frac{|P_{\text{now}} - P_{4\text{h}}|}{P_{4\text{h}}} \times 100 \ge 0.50\%$$
- **Mechanism**: The bot measures Bitcoin's net price movement over the trailing 16 15-minute candles (4 hours).
  - If trailing 4h movement is **$< 0.50\%$**, live entries are halted (`drawdown_4h_switch_gated`).
  - If trailing 4h movement is **$\ge 0.50\%$**, entries proceed.
- **Why this cuts drawdown**: Bypasses dead consolidation regimes where choppy back-and-forth price action causes consecutive whipsaw losses.
- **Additional Defenses**: Circuit Breaker (2 losses $\to$ 1h pause), Realized Volatility Gate ($< 45\text{ bp}$), and Skip Hours (18 & 23 UTC).
- **Micro-Vetoes DISABLED (`use_vetoes: false`)**.

---

## ⚡ GTD Low-Latency Execution Engine

- **Order Type**: Good 'Til Date (`OrderType.GTD`) limit orders with a **5-minute expiration timestamp**.
- **Signing Speed**: Sub-0.1ms C-accelerated EIP-712 signing via `coincurve`.
- **Pre-Warmed Token Caches**: Pre-fetched token IDs for instant zero-REST execution.
- **Reactive L2 WebSocket Feed**: Instant event-driven trade evaluation.
- **Default Port**: **`8085`**.

---

## 🏃 Running the Bot

```powershell
python main.py
```

Dashboard access:
👉 **`http://localhost:8085`**
