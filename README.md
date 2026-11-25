# E-commerce Sales Data Analytics Project (2024–2025)

## Project Overview

This project analyses a two-year e-commerce dataset (2024–2025) to understand sales performance, customer purchasing behaviour, product and category performance, and business priorities for the next year.

The work follows an end-to-end analytics workflow:

1. Inspect raw datasets and understand their structure.
2. Investigate duplicates and data-quality issues.
3. Clean and standardise the source tables.
4. Validate keys, relationships and numerical values.
5. Integrate orders, order items, products and customers.
6. Engineer analytical fields and define business metrics.
7. Perform exploratory data analysis (EDA).
8. Build visualisations and summarise business findings.
9. Develop practical recommendations.

## Business Questions

- How did revenue change between 2024 and 2025?
- What were the main observed drivers of the change?
- Which products, categories, payment methods, acquisition channels and customer groups contributed to performance?
- Where should the business focus next year?

## Dataset

The project uses four source CSV files:

| File | Description |
|---|---|
| `orders.csv` | Order date, customer, status, payment method, discount code and shipping fee |
| `order_items.csv` | Product-level order lines, quantity, historical unit price and discount amount |
| `products.csv` | Product name, category, catalogue list price and recorded unit cost |
| `customers.csv` | Customer signup date, state and acquisition channel |

**Analysis period:** 1 January 2024 – 31 December 2025  
**Integrated data:** 15,608 product-line rows, 9,207 unique orders, 90 products and 2,106 customers represented in the integrated data.

> The original dataset files are not included in this repository unless redistribution is permitted. Add the source files locally in the expected `data/raw/` directory before running the notebooks.

## Tools and Libraries

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook
- Git and GitHub

## Project Workflow

### 1. Data inspection

Inspected dataset dimensions, columns, data types, missing values, duplicates, unique identifiers, categorical values, numerical distributions and date ranges.

### 2. Duplicate investigation

Investigated potential duplicate order records. Identified and removed 38 confirmed duplicate order records, reducing the orders table from 9,245 to 9,207 rows.

### 3. Data consistency and cleaning

- Standardised product category labels to eight consistent categories: Kitchen, Bedding, Furniture, Decor, Outdoor, Bath, Lighting and Storage.
- Retained the three order statuses: `completed`, `canceled` and `returned`.
- Retained null `discount_code` values because they represent orders with no discount code.
- Checked key fields and numerical values for validity.

### 4. Missing historical unit prices

There were 199 missing `unit_price` values in the order-items data. These were reconstructed using observed transaction prices for the same product and year. The original observed prices were not changed, and a `price_was_missing` flag was retained.

These reconstructed values are estimates based on observed transactions, not independently verified original prices.

### 5. Numerical validation

Validated that:

- `quantity` > 0
- `unit_price` > 0
- `discount_amount` >= 0
- `discount_amount` does not exceed the gross line amount
- Net line amount is not negative

### 6. Data integration

Joined the cleaned tables using:

- `order_id` to connect order items with orders
- `product_id` to connect order items with products
- `customer_id` to connect orders with customers

All 15,608 order-item records matched in the reported integration checks. The integrated dataset contains 15,608 rows and 24 columns after analytical fields were added.

### 7. Feature engineering

Created:

```python
gross_amount = quantity * unit_price
net_amount = gross_amount - discount_amount
total_product_cost = quantity * unit_cost
gross_profit = net_amount - total_product_cost
gross_profit_margin = (gross_profit / net_amount) * 100
```

Also extracted `year`, `month` and `year_month` from `order_date`.

## Revenue Definition

The primary revenue metric is **completed-order net merchandise revenue**:

- Include completed orders.
- Exclude canceled orders.
- Exclude returned orders from primary revenue and analyse them separately.
- Calculate line-level net amount as quantity × historical unit price − line discount.
- Exclude shipping fees from merchandise revenue. Shipping fee is an order-level value repeated on item rows after joining, so it must not be summed directly at line level.
- Use historical transaction `unit_price`, not the current catalogue `list_price`.

Refund amounts were not available, so refunds were not estimated.

## Key Findings

### Revenue growth

| Metric | 2024 | 2025 | Change |
|---|---:|---:|---:|
| Net merchandise revenue | $850,786.45 | $1,031,187.83 | +21.20% |
| Completed orders | 3,867 | 4,589 | +18.67% |
| Average order value | $220.01 | $224.71 | +2.14% |

Revenue increased by **$180,401.38 (21.20%)** in 2025. Completed-order volume grew more strongly than AOV, suggesting order volume was the stronger observed driver. Every month and all eight categories recorded year-over-year revenue growth.

### Customer behaviour

| Measure | Result |
|---|---:|
| Customers with completed orders | 2,047 |
| Repeat customers | 1,318 (64.39%) |
| One-time customers | 729 (35.61%) |
| Revenue from repeat customers | $1,734,409.31 (92.16%) |
| Revenue from one-time customers | $147,564.97 (7.84%) |

Repeat customers generated the majority of completed-order revenue during the two-year period. Customer revenue totals are cumulative for the analysis window and are not formal customer lifetime value.

### Category performance

- Furniture had the highest two-year category revenue: **$322,848.81**.
- Storage had the highest estimated gross profit margin: **55.59%**.
- Bath had the lowest two-year category revenue: **$109,024.59**.
- Category decisions should consider revenue, gross profit dollars, margin and demand together.

### Gross profit estimate

| Measure | Result |
|---|---:|
| Net merchandise revenue | $1,881,974.28 |
| Recorded product cost | $956,026.02 |
| Estimated gross profit | $925,948.26 |
| Estimated aggregate gross profit margin | 49.20% |

Gross profit is estimated from recorded product costs. It is not net profit and excludes operating expenses, fulfilment, marketing and other overheads.

### Acquisition channels

- Organic search contributed the largest share of completed-order revenue: **$773,610.01 (41.11%)**.
- Paid search had the highest channel AOV: **$230.30**.
- Marketing spend was not available, so channel ROI or profitability cannot be calculated.

## Visualisations

The analysis produced six planned business charts:

1. Monthly revenue trend (2024 vs 2025)
2. Yearly revenue comparison
3. Category revenue comparison
4. Top 10 products by revenue
5. Category gross profit margin
6. One-time vs repeat customer revenue contribution

Chart exports are intended to be stored in `output/chart/`. Confirm the generated PNG files are present before publishing the repository.

## Business Recommendations

### 1. Improve repeat purchasing

Create a targeted post-purchase programme for one-time customers using relevant follow-up communication, complementary product recommendations and carefully tested second-purchase incentives.

Measure second-purchase conversion, repeat purchase rate, time to second order, incremental revenue and gross profit after incentive costs. Use a control group where possible to test whether the programme creates incremental purchases.

### 2. Optimise category profitability

Review Furniture product costs, pricing and discounting because it generated the highest category revenue but a lower estimated margin than Storage. Investigate Storage products with stronger margins and test selective opportunities to grow them. Review Bath at product level before making assortment changes.

Measure revenue, gross profit dollars, gross profit margin, units sold, discount rate and return rate. Do not make major inventory or pricing decisions from margin alone.

## Repository Structure

A suggested structure for the GitHub repository:

```text
ecommerce-sales-data-analysis/
│
├── data/
│   ├── raw/                  # Original CSV files (not committed if restricted)
│   ├── cleaned/              # Cleaned CSV files
│   └── processed/            # Integrated analysis dataset
│
├── notebooks/
│   ├── data_inspection(1).ipynb
│   ├── investigate_and_handle_duplicated_records.ipynb
│   ├── check_data_consistency.ipynb
│   ├── 04_data_preparation_integration.ipynb
│   └── 05_exploratory_data_analysis.ipynb
│
├── output/
│   ├── chart/                # Exported PNG charts
│   └── reports/              # Final project reports
│
├── README.md
└── .gitignore
```

The structure above is a recommended layout. Adjust filenames and folders to match the actual repository. During development, the integrated CSV was referenced from both `data/processed/` and `data/cleaned/` in different steps. Before publishing, choose one canonical path and update notebook paths and this README consistently.

## How to Run the Project

1. Clone or download the repository.
2. Create and activate a Python virtual environment (recommended).
3. Install the required packages:

   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```

4. Place the source CSV files in the expected raw-data folder, if permitted and available.
5. Run the notebooks in workflow order.
6. Confirm that cleaned and integrated datasets are generated at the paths used by subsequent notebooks.
7. Run the EDA notebook and confirm that all chart images are saved under `output/chart/`.

The notebook paths and source data locations may need adjustment to match your local setup.

## Limitations and Assumptions

- 199 missing historical unit prices were reconstructed using observed product-year transaction prices and flagged.
- Recorded `unit_cost` was used for gross profit estimates; historical cost accuracy was not independently verified.
- Gross profit excludes operating expenses and other overheads.
- Shipping fees were excluded from merchandise revenue to avoid double-counting repeated order-level values.
- Refund values were not available for returned orders.
- Marketing spend was unavailable, so acquisition-channel ROI cannot be measured.
- Customer revenue is cumulative over the two-year period and is not formal lifetime value.
- The analysis is descriptive and does not establish causation.

## Conclusion

The project converted four related e-commerce datasets into an integrated analytical dataset and used it to assess revenue, orders, customers, products, categories, payment methods and acquisition channels.

Completed-order net merchandise revenue grew by **21.20%** from 2024 to 2025. Growth was accompanied by an **18.67% increase in completed orders** and a **2.14% increase in AOV**. Repeat customers contributed **92.16% of revenue**, while category analysis showed that the highest-revenue category was not the highest-margin category.

The recommended priorities for the next year are to improve repeat purchasing and optimise category profitability using measured, product-level tests.

---

*This project is intended for learning and portfolio demonstration. All conclusions are based on the supplied dataset and the assumptions documented above.*
