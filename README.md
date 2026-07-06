<img width="882" height="221" alt="Screenshot 2026-07-06 151334" src="https://github.com/user-attachments/assets/201a33cc-0182-427d-b647-f3c8ea88d836" />



# Global Electronics Retailer — Sales Decline & Demand Analysis

Business-focused analysis of a global electronics retailer's sales collapse, using SQL and Power BI to separate demand-side drivers from operational ones and turn the findings into recovery recommendations.

An end-to-end analysis project for a global electronics retailer, built with **SQL Server** (data cleaning, modeling, exploration) and **Power BI** (data modeling, DAX measures, and dashboarding).

## Problem Statement

The retailer experienced consistent growth in sales and order volume from 2016 to 2019, reaching peak performance in 2019. From 2020 onward, both sales and order volume declined sharply. Initial checks showed delivery performance actually improved over this period, ruling out operational inefficiency as the cause. This project digs into sales channels, product categories, and regional performance to identify the real drivers of the decline and support business recovery.

## Objectives

1. Analyze sales and order volume trends over time (2016–2021)
2. Evaluate Average Order Value (AOV) to see if customers were spending less per order
3. Assess delivery performance to rule in/out operational causes
4. Compare online vs. in-store sales channel performance
5. Break down performance by product category and region
6. Identify seasonality patterns and disruptions
7. Deliver data-driven recommendations for recovery

## Project Structure

```
├── Dataset Copy/          # Raw dataset (Sales, Customers, Products, Stores, Exchange_Rates)
├── SQL/                   # Data cleaning, standardization & exploratory analysis scripts
├── Power BI/              # Data model, DAX measures & dashboard file
└── README.md
```

## Tools & Tech Stack

- **SQL Server** – data cleaning, type/date casting, deduplication, derived columns
- **Power BI (Power Query + DAX)** – data modeling, calculated measures, dashboarding
- **Excel** – supporting checks and quick calculations

## Dataset Overview

62,884 records across 37 fields, spread over 5 related tables:

| Table | Key Fields | Description |
| --- | --- | --- |
| **Sales** | Order Number, Line Item, Order Date, Delivery Date, CustomerKey, StoreKey, ProductKey, Quantity, Currency Code | Fact table — one row per line item; `(Order Number, Line Item)` is the composite primary key |
| **Customers** | CustomerKey, Gender, Name, City, State, Zip Code, Country, Continent, Birthday | Customer demographics; `CustomerKey` is primary key |
| **Products** | ProductKey, Product Name, Brand, Color, Unit Cost USD, Unit Price USD, SubcategoryKey, Subcategory, CategoryKey, Category | Product catalog; `ProductKey` is primary key (Subcategory/Category keys contain duplicates, so they aren't used as PK) |
| **Stores** | StoreKey, Country, State, Square Meters, Open Date | Store metadata; `StoreKey` is primary key |
| **Exchange Rates** | Date, Currency, Exchange | Daily FX rates vs. USD; `(Currency, Date)` is the composite primary key |

**Data Model**
- `CustomerKey`, `StoreKey`, `ProductKey`, `Currency Code`, `Order Date` in Sales are foreign keys
- `Currency Code` + `Order Date` together form a one-to-many relationship into Exchange Rates (individually they'd be many-to-many)
- Customers, Products, Stores, and Exchange Rates have no foreign keys of their own

## Data Cleaning (SQL)

- **Delivery Date** was ~80% blank (cancelled/incomplete/untracked orders) — left as-is rather than imputed, since mean/median filling would fabricate delivery activity that never happened. Used only where necessary, and a derived **Delivery Days** column (`Delivery Date − Order Date`) was created instead
- Fixed inconsistent date formats in **Order Date** and **Delivery Date** (Sales), **Birthday** (Customers), and **Date** (Exchange Rates) using `TRY_CONVERT()`
- **Products**: cast `Unit Cost USD` / `Unit Price USD` to float after stripping the `$` sign; standardized `Subcategory` text (`&` → `and`)
- **Stores**: handled blanks in `Square Meters`; fixed `Open Date` inconsistencies

## Key Metrics Tracked (Power BI)

- **Order Volume** and **Revenue** by year, region, category, and channel
- **Average Order Value (AOV)** — online vs. in-store
- **Delivery Days** (`Delivery Date − Order Date`), used only where delivery data existed
- **Unit Price / Unit Cost trend** over time

## Dashboard Pages (Power BI)

1. **Sales & Order Volume Trend** – line chart, 2016–2021, to establish the growth-then-decline pattern
2. **AOV Trend** – line chart tracking spend per order over time
3. **Category-wise Sales** – stacked bar of revenue by product category over time
4. **Country-wise Sales** – stacked bar of revenue by country/region over time
5. **Product Price Trend** – line chart of unit price/cost movement
6. **Channel Analysis** – Instore vs. Online revenue and AOV comparison
7. **Monthly Seasonality** – month-over-month sales, compared year to year
8. **Delivery Performance** – average delivery days by year

## Key Findings

- **Sales and order volume peaked in 2019** (9,083 orders, $18.31M revenue) and then collapsed — 2021 hit a low of 498 orders and $1.02M, a 96% drop in order volume from the prior year
- **Orders and revenue move together almost identically**, which points to a genuine drop in customer demand rather than a pricing or supply issue
- **Delivery performance improved, not worsened**, over the decline (avg. ~7 days in 2016 down to ~4 days by 2021) — ruling out operational inefficiency as a cause
- **AOV declined only moderately** (in-store $2,116.9 vs. online $2,048.6 — a $68.3 gap), acting as a secondary contributor rather than the main driver
- **In-store sales fell much more sharply than online sales**, indicating a channel shift consistent with reduced footfall
- **Seasonal peaks weakened in 2020** — the usual February/December spikes flattened out, signaling a broader disruption in buying patterns
- **Non-essential categories like Home Appliances declined more** than others, while categories tied to remote work/life held up better
- **Unit price and unit cost rose until 2019, then fell** — their trend mirrors the orders/revenue trend, reinforcing that this is a demand-side story, not a pricing-side one

## Probable Root Cause

The timing and shape of the decline align with the global disruption caused by COVID-19: delayed non-essential purchases, prioritization of essentials over electronics, and reduced discretionary spending overall — compounded by a shift away from physical stores during lockdowns.

## Questions Answered

| Question | Answer |
| --- | --- |
| Which products/categories have the highest demand? | Computers, Home Appliances, Cameras |
| Which countries/regions generate the most orders? | USA (California, Texas, New York), Canada (Ontario) |
| How does demand change over time? | Grows steadily to 2019, then declines sharply from 2020 |
| Which customer segments buy the most? | Elderly (75+), Older Adults (45+) |
| Are there seasonal trends in demand? | Peaks in Feb & Dec through 2019; no December peak from 2020 onward |
| Which products are declining or growing? | Computers, Home Appliances, and Cell Phones grew through 2019; Home Appliances declined afterward |

## Business Recommendations

1. **Strengthen online sales channels** to reduce dependency on physical stores
2. **Prioritize USA and Canada** — the highest-volume markets — for inventory and marketing investment
3. **Upsell & cross-sell**: Computers is the best-selling category — steer customers toward higher-value subcategories within it, and bundle related products at a slight discount to lift AOV
4. **Introduce promotions during identified low-demand periods** to stabilize order volume
5. **Improve supply chain resilience** so the business can absorb future demand shocks without operational strain

## Summary

The post-2019 decline is primarily **demand-driven** — order volume and revenue move together and both collapsed at the same time — with a gradual decline in AOV and a shift away from in-store purchases contributing secondarily. Delivery performance actually improved over the same period, ruling out operational issues as a cause. The pattern is consistent with reduced discretionary spending during the COVID-19 pandemic.

## How to Reproduce

1. Import the tables from `Dataset Copy/` into SQL Server
2. Run the cleaning scripts in `SQL/` to standardize dates, cast currency fields, and derive `Delivery Days`
3. Load the cleaned tables into Power BI and build the model using the primary/foreign key relationships above
4. Add the measures listed under **Key Metrics Tracked**
5. Rebuild the dashboard pages: Sales & Order Volume Trend, AOV Trend, Category/Country-wise Sales, Product Price Trend, Channel Analysis, Monthly Seasonality, Delivery Performance

# Dashboard Preview
<img width="1306" height="737" alt="Screenshot 2026-04-14 184043" src="https://github.com/user-attachments/assets/cc207103-a2f6-4c32-8031-a56a3d4495ef" />




