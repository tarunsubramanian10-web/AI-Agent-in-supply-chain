# Inventory Replenishment Agent

An autonomous supply-chain agent that forecasts demand with EWMA, computes dynamic safety stock and reorder points, and places purchase orders using an (s, S) inventory policy to minimise total cost while maintaining a target Cycle Service Level (CSL).

## Repository Structure

```
inventory-replenishment-agent/
├── inventory_replenishment_agent.ipynb   # Main notebook (run this)
├── data/
│   ├── sales.csv       # Daily units sold per SKU (120 days × 3 SKUs)
│   ├── inventory.csv   # Opening on-hand stock and unit cost per SKU
│   └── params.csv      # Policy parameters per SKU (lead time, CSL, costs, α)
├── requirements.txt
└── README.md
```

## Quickstart

### Google Colab
1. Upload the entire repo folder (or clone it).
2. In the first code cell, add and run:
   ```python
   !pip install -q "pydantic>=2.0"
   ```
   Then **Runtime → Restart session**.
3. Run all cells (`Runtime → Run all`).

### Local
```bash
pip install -r requirements.txt
jupyter notebook inventory_replenishment_agent.ipynb
```

## Agent Design

| Component | Detail |
|-----------|--------|
| **Forecast** | EWMA with per-SKU smoothing factor α; updates online every day |
| **Policy** | (s, S) — order up to S_max whenever Inventory Position ≤ dynamic ROP |
| **Safety stock** | SS = z(CSL) × σ_LT, where σ_LT is derived from the EWMA error variance |
| **Order cap** | Max single order = 5 × EOQ (guardrail) |
| **Daily log** | Every decision printed with IP, ROP, forecast, on-hand, backlog |

## Key Results (90-day simulation, 3 SKUs)

| SKU | Policy | Fill Rate | Total Cost |
|-----|--------|-----------|------------|
| SKU-A | Agent (s,S) + EWMA | 97.99% | $1,649 |
| SKU-A | Baseline (s,Q) | 96.94% | $1,779 |
| SKU-B | Agent (s,S) + EWMA | 99.21% | $1,198 |
| SKU-B | Baseline (s,Q) | 100.00% | $1,092 |
| SKU-C | Agent (s,S) + EWMA | 96.01% | $2,164 |
| SKU-C | Baseline (s,Q) | 95.84% | $2,143 |

## Dependencies

See `requirements.txt`. All packages are available on Google Colab except Pydantic v2 (install instruction above).
