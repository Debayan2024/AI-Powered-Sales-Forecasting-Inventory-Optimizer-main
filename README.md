# 📈 Sales Forecasting & Inventory Optimization — Superstore Analytics

**Forecasting sales with Prophet and turning it into a Power BI dashboard someone could actually use to plan inventory**

---

## Why I built this

A lot of retail/inventory decisions get made by looking at last month's numbers and guessing. I wanted to see how much better that gets if you actually forecast where sales are headed, and then pair that with a clear picture of which categories are making money vs. just moving volume. I used the Superstore dataset (2014–2018, order-level transactions) as a stand-in for that kind of retail data.

Basically: *can a forecast + a profit breakdown give someone enough to make a real stocking decision, not just a nice chart?*

---

## The data

Order-level Superstore data — Order ID, dates, customer, segment, region, category/sub-category, sales, quantity, discount, profit. About 9,800 rows after cleaning, spanning 2014–2018.

I added a couple of fields myself: `Year-Month` (for aggregating by month) and `Shipping Time` (days between order and ship date).

Flow: raw CSV → clean/dedupe → `Cleaned_Superstore.csv` → Prophet forecast → `sales_forecast.csv` → Power BI dashboard (`AI_Forecast_Dashboard.pbix`).

---

## What I actually did

**Cleaning first.** Checked dtypes and nulls, stripped whitespace out of column names (always something), dropped duplicate rows, parsed the date columns properly.

**Looked at the data before modeling anything.** Category order volume, monthly sales trend, which sub-categories actually sell the most units. This mattered more than I expected — the monthly trend plot is what made it obvious there's a strong seasonal spike every Q4, which shaped how I thought about the forecast horizon later.

**Forecasting with Prophet.** Aggregated to daily total sales, fit a basic additive Prophet model (trend + weekly + yearly seasonality), and forecasted 90 days out with uncertainty bounds. Nothing fancy — no extra regressors yet, just date-based seasonality.

**Then the dashboard.** Took the forecast and the cleaned data into Power BI so the output isn't just a notebook. KPI summary up top (sales, profit, quantity), a forecast-vs-actual line so you can actually judge how good the model is instead of just trusting it, and a profit drilldown by category → sub-category → segment, since "will we sell more" and "where's the profit" are two different questions and you need both for an inventory call.

---

## What it showed

- **$2.30M** total sales, **$286.4K** profit, **38K** units over the period
- Sales spike hard every Q4 — if this were a real inventory decision, you'd want to be building stock 6-8 weeks ahead of that, not reacting to it
- **Technology** is the strongest category for profit, **Consumer** the strongest segment — those two are where I'd prioritize stocking if I only had budget for one bet
- The forecast tracks actuals pretty well in normal months, but it clearly misses around promotional spikes — worth saying that out loud rather than pretending the chart is perfect

(Screenshot's in `AI_Sales_Forecast_Dashboard.png`, or open the `.pbix` in Power BI Desktop to poke around yourself.)

---

## What I'd fix if I kept going

Being honest about the gaps here on purpose — I think it says more than pretending the model's done:

- The Prophet model only knows about dates right now. Discounts, holidays, and promotions are obvious drivers of the variance it's missing.
- One global model glosses over the fact that Furniture and Technology probably don't move the same way seasonally. Splitting by category would probably help.
- I haven't actually backtested this — no MAPE/MAE, just eyeballing the chart. That's the next thing I'd add before trusting this for a real decision.
- Turning the forecast into an actual reorder-point recommendation would need lead time and holding cost numbers I don't have yet, but that's the real endpoint of a project like this.

---

## Repo

```
├── _Sales_Forecasting___Inventory_Optimization.ipynb   # cleaning, EDA, Prophet model
├── Cleaned_Superstore.csv                              # cleaned dataset
├── sales_forecast.csv                                  # Prophet output
├── AI_Forecast_Dashboard.pbix                          # Power BI dashboard
├── AI_Sales_Forecast_Dashboard.png                     # dashboard screenshot
└── README.md
```

## Tools

Python (Pandas, Prophet, Matplotlib, Seaborn) for the analysis, Power BI for the dashboard, Jupyter for the notebook.

## Running it

```bash
pip install pandas prophet matplotlib seaborn
jupyter notebook "_Sales_Forecasting___Inventory_Optimization.ipynb"
```
Then open the `.pbix` in Power BI Desktop, pointed at `sales_forecast.csv` and `Cleaned_Superstore.csv`.

---

## About me

**Mehfil** — B.Tech in AI & Data Science, based in India, looking at Data Analyst roles (open to the UAE).
