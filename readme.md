# SPY Options Pricing Engine (Model‑Free)

Turn listed **SPY call options** into a **model‑free pricing and hedging engine**. From today’s SPY call prices we recover the implied risk‑neutral distribution, price custom payoffs, replicate them statically with listed calls, and check for cross‑expiry inconsistencies.

---

## Investment goal
- **Design & hedge bespoke payouts** (e.g., digitals, capped returns, overlays) using only liquid SPY options.
- **Value and compare** payoffs **without** assuming Black‑Scholes or a specific vol model.
- **Find edges**: detect **no‑arbitrage violations** (esp. across nearby expiries) and **relative value** (e.g., a quoted digital richer than its cheapest super‑hedge).

---

## What the project does
1. **Clean the SPY call curve** (enforce decreasing & convex w.r.t. strike).
2. Extract the **risk‑neutral PDF** via Breeden–Litzenberger (second derivative of call prices).
3. **Replicate any payoff** with a static mix of **cash + forward + a strip of calls**; output weights & errors.
4. Compute **model‑free price bounds** (super/sub‑hedges) via LP duality.
5. Run **Monte Carlo from the implied PDF** to stress‑check prices.
6. Perform a **calendar‑arbitrage check** by comparing normalized call curves across two nearby expiries.

> We focus on **SPY** for liquidity and dense strikes. Expiry choice is flexible; tools work for any single date and for pairs in the calendar check.

---

## Why this matters
- **Transparent**: prices/weights come straight from traded options and no‑arb math.
- **Actionable**: buildable with listed SPY options; detect cross‑term mispricings.
- **Lightweight**: no heavy simulation to get started; Monte Carlo used only for validation.

---

## Outputs
- **Cleaned call curve** & **implied PDF** (CSV).
- **Replication weights** (CSV) and **fit metrics** (RMSE, P95, etc.).
- **Model‑free bounds** (JSON) and **super/sub hedge portfolios** (CSV).
- **MC prices** for chosen payoffs and a **calendar‑arb summary** (JSON) across two expiries.

---

## Notebook map
- **01 — Clean curve & implied PDF**: loads SPY chain, projects to a convex‑monotone call curve, exports the risk‑neutral PDF.
- **02 — Static replication**: fits cash+forward+calls to your payoff; supports **OLS** (signed weights) and **NNLS** (non‑negative). Digitals ≈ tight call spread.
- **03 — Model‑free bounds**: solves super/sub‑hedge LPs to produce **no‑arb price interval** and hedge portfolios. Includes stability/shape constraints.
- **04 — MC & calendar check**: samples from the implied PDF for pricing sanity checks and flags **calendar violations** using forward‑moneyness normalization.

---

## Quick start
1. **Data**: use any recent SPY option chain. (Sample data included; live fetch optional.)
2. Run **Notebook 01** → produces `cleaned_chain_*.csv` and `implied_pdf_*.csv`.
3. Run **Notebook 02** to replicate a payoff (e.g., digital at ATM). For non‑convex payoffs, set `FIT_MODE='ols'`.
4. Run **Notebook 03** for model‑free bounds; check solver status and constraint slack.
5. Run **Notebook 04** for MC validation and calendar‑arb diagnostics.

---

## Caveats
- **Risk‑neutral** probabilities include risk premia; they are not literal real‑world odds.
- Replications are **static** (no re‑hedging mid‑life).
- Garbage‑in, garbage‑out: bad quotes can create spurious "arbs"; the cleaning step matters.

---

## TL;DR
For **SPY**, this gives a fast, transparent way to **read the market’s distribution**, **price & build custom payoffs from listed options**, and **spot cross‑expiry inconsistencies**—with minimal assumptions and compute.
