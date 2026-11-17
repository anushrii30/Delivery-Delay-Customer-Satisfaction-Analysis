# Delivery Delay & Customer Satisfaction Analysis

**Tools:** Python (Pandas, Matplotlib) · MySQL · Power BI
**Dataset:** [Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (Kaggle)

## Business question

Why are some orders delivered late, and what could the company do to reduce it?

Every order has a *promised* delivery date and an *actual* delivery date. I measured the gap between them, found where late deliveries concentrate, and measured what they cost in customer satisfaction. The planned-vs-actual comparison is the same idea as schedule variance in project analytics.

## Key findings

| # | Finding | Evidence |
|---|---|---|
| 1 | **6.8% of delivered orders arrived late**, yet the median order arrived **12 days early** | 96,470 delivered orders |
| 2 | **Estimates are heavily padded.** Promised delivery time was about **2x** the typical actual time | Median promised 23 days vs. median actual 10 days |
| 3 | **Late delivery sharply hurts satisfaction** | Average review score **4.29 (on time) vs. 2.27 (late)** |
| 4 | **Lateness is geographic.** The worst states are in the north and northeast | Alagoas 21.4%, Maranhão 17.4%, Sergipe 15.2% late |
| 5 | **Lateness is concentrated in a few sellers.** Top 10% of sellers account for **74%** of late orders | Worst sellers with 30+ orders were 20-33% late |
| 6 | In the worst states, deliveries are slower *and* the estimate is tighter | Alagoas: 24.0 days actual vs. 32.2 promised |

## Recommendations

1. **Work with the highest-late-rate sellers first** (coaching, penalties, or removal from fast-delivery programs). A small group drives most of the problem.
2. **Set longer, more realistic estimates for the north and northeast** and review carrier options there.
3. **Shorten estimates in fast regions.** Overly padded promises make the estimate less useful to customers.

## Approach

1. **Loaded and explored** four tables: orders, customers, order items, and reviews.
2. **Cleaned the data:**
   - Kept only orders with status `delivered` and a recorded delivery date (96,470 of 99,441 orders). The 2,971 orders removed were cancelled, in transit, or missing a delivery date.
   - Converted text timestamps to real dates.
   - Removed duplicate reviews (some orders had two) so orders were not double counted.
3. **Created metrics:**
   - `delay_days` = actual delivery date minus estimated delivery date (positive = late)
   - `is_late` = `delay_days` > 0
   - `actual_days` and `estimated_days` = days from purchase to delivery, actual and promised
4. **Analyzed** lateness over time, by customer state, against review scores, and by seller (sellers with at least 30 orders, to avoid misleading small samples).
5. **Repeated the core analysis in SQL (MySQL)** to cross-check the results.
6. **Built a Power BI dashboard** for non-technical users.

## SQL analysis (MySQL)

The same questions answered with SQL: delivered-order count and late %, late % by state (`JOIN` + `GROUP BY`), review score for on-time vs. late orders, and the worst sellers (`HAVING COUNT(*) >= 30`).

- Queries: [`sql/`](sql/) *(add the link to your folder)*
- The SQL results match the pandas results (96,470 delivered orders, 6.8% late).

## Power BI dashboard

An interactive one-page dashboard with:
- KPI cards: total orders, late-delivery %, average review score
- Late % by customer state
- Late % by month
- Average review score, on time vs. late
- A state slicer that filters every visual

![Dashboard](images/dashboard.png) *(add your screenshot)*

## Repository structure

```
├── notebooks/        # Python analysis (delivery_analysis.ipynb)
├── sql/              # MySQL queries
├── dashboard/        # Power BI file (.pbix) and screenshot
├── data/             # dashboard_data.csv (the raw Olist CSVs are not included; see the Kaggle link)
└── README.md
```

## How to reproduce

1. Download the dataset from Kaggle and place the CSVs next to the notebook.
2. `pip install pandas matplotlib jupyter`
3. Run `delivery_analysis.ipynb` from top to bottom.
4. For SQL: import the CSVs into a MySQL schema and run the files in `sql/`.

## Limitations and next steps

- **Seller concentration is partly a volume effect.** High-volume sellers naturally have more late orders in raw counts. The 74% figure shows that lateness is concentrated, but ranking by late *rate* (with a minimum order count) is the fairer way to identify problem sellers, and the table above does this for the top 10.
- **Causes are not established.** The data shows *where* and *how much*, not *why*. Distance, carrier performance, and product size are likely drivers that I did not model.
- **Orders that were never delivered are excluded**, so the late rate here may understate the real customer experience.
- **Next step:** build a state-level delivery estimator, train it on earlier months and test it on later ones, and measure how much it improves on the company's own estimates.

## About

Built as a data analytics portfolio project. Questions or feedback are welcome.
