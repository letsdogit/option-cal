# ================================
# ⚡ Streamlit App: Option Manipulation Simulation
# -------------------------------
# Shows how an illiquid option can be manipulated by an algo acting as both buyer & seller
# ================================

import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

st.set_page_config(page_title="Option Manipulation Simulator", layout="wide")

st.title("🎯 Option Manipulation Scenario (Algo vs Human)")
st.markdown(
    """
    This simulation demonstrates how, in a **non-liquid option**, an **algorithm** acting as both buyer and seller  
    can manipulate prices to trap human traders.
    """
)

# --- Simulation Parameters ---
col1, col2 = st.columns(2)
with col1:
    fair_price = st.slider("Fair Price of Option", 20, 100, 40)
    algo_bid = st.slider("Algo Bid Price", 0, 50, 20)
    algo_ask = st.slider("Algo Ask Price", 50, 120, 80)
    human_limit_buy_price = st.slider("Human Limit Buy Price", 0, 50, 21)
with col2:
    pump_start_price = st.slider("Algo Pump Start Price", 0, 50, 22)
    pump_step = st.slider("Pump Step", 0.5, 5.0, 1.0)
    pump_trade_size = st.slider("Pump Trade Size", 5, 50, 10)
    normal_buyer_size = st.slider("Normal Buyer Size", 10, 100, 30)

# --- Simulation Logic ---
sell_to_normal_threshold = fair_price * 1.20
events = []
time = 0

events.append({
    "time": time, "actor": "algo", "action": "quote",
    "price": f"bid={algo_bid},ask={algo_ask}", "note": "initial quotes"
})
time += 1

events.append({
    "time": time, "actor": "human_limit_buy", "action": "limit_buy",
    "price": human_limit_buy_price, "note": "human places passive buy at 21"
})
time += 1

price = pump_start_price
while price <= fair_price:
    events.append({
        "time": time, "actor": "algo", "action": "buy",
        "price": price, "note": "algo pumps price (wash trade)"
    })
    time += 1
    price += pump_step

events.append({
    "time": time, "actor": "momentum_buyer", "action": "market_buy",
    "price": fair_price + 2, "note": "momentum buyer sees rising price"
})
time += 1

events.append({
    "time": time, "actor": "algo", "action": "sell",
    "price": sell_to_normal_threshold, "note": "algo sells 20% above fair value"
})
time += 1

events.append({
    "time": time, "actor": "algo", "action": "quote",
    "price": f"bid={algo_bid},ask={algo_ask}", "note": "restores passive quotes"
})

df = pd.DataFrame(events)

# --- Plot Price Evolution ---
trade_rows = df[df["action"].isin(["buy", "sell", "market_buy"])]
price_series = trade_rows[["time", "price"]].copy()
price_series["price"] = price_series["price"].astype(float)

fig, ax = plt.subplots(figsize=(10, 4))
ax.plot(price_series["time"], price_series["price"], marker="o", color="tab:blue", label="Last Trade")
ax.axhline(fair_price, color="green", linestyle="--", label="Fair Price")
ax.axhline(sell_to_normal_threshold, color="red", linestyle="--", label="20% Above Fair")
ax.set_xlabel("Time")
ax.set_ylabel("Trade Price")
ax.set_title("Last Traded Price Over Time")
ax.legend()
ax.grid(True)
st.pyplot(fig)

# --- Results Summary ---
unrealized_loss = sell_to_normal_threshold - fair_price
st.subheader("📊 Summary")
st.markdown(
    f"""
    - **Fair price:** ₹{fair_price:.2f}  
    - **Algo passive quotes:** bid ₹{algo_bid}, ask ₹{algo_ask}  
    - **Algo pumped** the price from ₹{pump_start_price} → ₹{fair_price}.  
    - **Momentum buyer** entered and bought at **₹{sell_to_normal_threshold:.2f}** (20% above fair).  
    - **Unrealized loss** to normal buyer = ₹{unrealized_loss:.2f} per unit.  
    - After selling, the algo reset back to **bid ₹{algo_bid} / ask ₹{algo_ask}**, trapping the human at a loss.
    """
)

st.dataframe(df, use_container_width=True)

st.info(
    "💡 In low-liquidity options, such manipulation is possible because the same participant can control both sides "
    "of the order book (buyer & seller) and lure momentum buyers into overpriced trades."
)

st.markdown("---")
st.caption("Developed by Divyanshu Jain — educational simulation for understanding illiquid option dynamics.")
