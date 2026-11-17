# OrderBook-Oracle 📈

**Market Microstructure, Limit Order Book Matching Engine & Avellaneda–Stoikov Inventory-Aware Market Making**

`OrderBook-Oracle` is a quantitative finance and market microstructure project implemented in Python. It explores how modern electronic exchanges match orders and how quantitative market makers dynamically price liquidity while managing inventory and adverse-selection risk.

---

## 📂 Repository Structure

```text
OrderBook-Oracle/
├── LOB_Assignment1.ipynb               # Limit Order Book (LOB) Engine, Matching, VWAP, Slippage & Depth Charts
├── Avellaneda_Stoikov_Assignment.ipynb # Avellaneda–Stoikov (2008) Market Maker, Monte Carlo & Sensitivity Analysis
├── LimitOrderBook (1).pdf              # Reference paper: "High-frequency trading in a limit order book" (Avellaneda & Stoikov)
└── README.md                           # Project documentation
```

---

## 🧩 Module 1: Limit Order Book (LOB) & Matching Engine (`LOB_Assignment1.ipynb`)

A step-by-step implementation of a **Price/Time Priority (FIFO)** Continuous Double Auction Limit Order Book from scratch:

1. **Order Representation & Lifecycle**
   - Standardized order structure tracking `order_id`, `side` (`BID` / `ASK`), `quantity`, `price`, `filled_qty`, and `remaining_qty`.
2. **Two-Sided Order Book (`OrderBook`)**
   - Maintains descending price priority for **Bids** (highest buyer first) and ascending price priority for **Asks** (lowest seller first), preserving FIFO arrival priority within each price level.
   - Real-time top-of-book queries: `best_bid()`, `best_ask()`, `spread()`, `mid_price()`, `cancel_order()`, and book ladder visualization (`display()`).
3. **Matching Engine & Execution Analytics**
   - Spread-crossing detection (`is_aggressive()`) and multi-level sweep execution (`match_order()`) supporting partial fills, passive resting of residual volume, and full trade logging.
   - **Volume-Weighted Average Price (VWAP)** and **Execution Slippage (bps)** analysis demonstrating book-walking costs in thin vs. deep order books.
4. **Market Metrics & Visualization**
   - Order book depth snapshots via `pandas.DataFrame` (`book_to_dataframe()`).
   - Cumulative liquidity **Market Depth Charts** (`plot_depth_chart()`) and empirical demonstration of **Price Impact** following large aggressive sweeps.
5. **Exchange Economics & High-Performance Systems Design**
   - **Maker–Taker Fee & Rebate Calculator** quantifying net execution costs and liquidity-provision rebates.
   - Architectural blueprint for a production $O(1)$ matching engine combining a **Sorted Price Map / Heap**, **Doubly-Linked FIFO Queues**, and an **Order-ID Hash Map**.

---

## 🎯 Module 2: Avellaneda–Stoikov Market Making (`Avellaneda_Stoikov_Assignment.ipynb`)

A complete implementation and stress-test of the seminal **Avellaneda & Stoikov (2008)** inventory-aware market-making framework (*"High-frequency trading in a limit order book"*):

### Core Mathematical Framework

1. **Mid-Price Dynamics (Driftless Arithmetic Brownian Motion)**
   $$dS_t = \sigma\,dW_t$$
2. **Reservation (Indifference) Price**
   $$r(s, q, t) = s - q\,\gamma\,\sigma^2\,(T - t)$$
   Skews quotes away from the market maker's current inventory $q$: quoting lower when long ($q > 0$) to encourage selling, and higher when short ($q < 0$) to encourage buying.
3. **Optimal Bid–Ask Spread**
   $$\delta(t) = \underbrace{\gamma\,\sigma^2\,(T - t)}_{\text{inventory-risk term}} + \underbrace{\frac{2}{\gamma}\ln\!\left(1 + \frac{\gamma}{k}\right)}_{\text{order-flow term}}$$
   $$\text{bid} = r - \frac{\delta}{2}, \qquad \text{ask} = r + \frac{\delta}{2}$$
4. **Poisson Order Arrivals with Exponential Intensity Decay**
   $$\lambda(\delta) = A\,e^{-k\delta}$$

### Key Experiments & Findings

- **Single-Path & Monte Carlo Evaluation (300+ Paths):** Demonstrates how reservation-price skewing mean-reverts inventory $q$ around zero and produces a tightly concentrated, consistently positive PnL distribution ($\text{Win Rate} \approx 100\%$, $\text{Sharpe} \approx 5.5+$).
- **Risk-Aversion ($\gamma$) Sensitivity Sweep:** Quantifies the fundamental trade-off between **turnover** (highest mean PnL at low $\gamma$) and **inventory control** (lowest $\text{std}(q)$ at high $\gamma$), showing why risk-adjusted return ($\text{mean}(\text{PnL}) / \text{std}(\text{PnL})$) peaks at an interior optimum ($\gamma \approx 0.05\text{–}0.10$).
- **Desk Extensions (Part 9):**
  - **Hard Inventory Caps (`q_max`):** Pulling quotes on the position-worsening side when $|q| \ge q_{\max}$ strictly bounds tail inventory risk.
  - **Adverse Selection / Unmodeled Drift:** Demonstrates how toxic/trending order flow degrades market-making PnL when the mid-price violates the driftless assumption.

---

## 🚀 Getting Started

### Prerequisites

Ensure you have **Python 3.9+** installed along with the standard scientific stack:

```bash
pip install numpy pandas matplotlib jupyter
```

### Running the Notebooks

Launch Jupyter Notebook or JupyterLab from the repository root:

```bash
jupyter notebook
```

Open either notebook and run all cells top-to-bottom:
- `LOB_Assignment1.ipynb`
- `Avellaneda_Stoikov_Assignment.ipynb`

---

## 📚 References & Learning Resources

1. **Primary Paper:**
   - Marco Avellaneda & Sasha Stoikov (2006 / 2008), *"High-frequency trading in a limit order book"*, Quantitative Finance, 8(3), 217–224. Included in this repository as `LimitOrderBook (1).pdf`.
2. **Limit Order Book Mechanics:**
   - [An Introduction to Limit Order Books — Michael Chow (machow.ski)](https://www.machow.ski/posts/2021-07-18-introduction-to-limit-order-books/#marketorder)
3. **Video Lectures & Deep-Learning LOB Extensions:**
   - [Intuitive Introduction to Limit Order Books — Part 1](https://youtu.be/BJt6PGKeO9I)
   - [Intuitive Introduction to Limit Order Books — Part 2](https://youtu.be/mUvB89DMtww)
   - [Deep Learning for Limit Order Books — Part 1](https://youtu.be/yqccDoujAUc)
   - [Deep Learning for Limit Order Books — Part 2](https://youtu.be/qxSh2VFmRGw)
