# Olist Marketing Funnel & Customer Retention Analysis

## Project Overview

This project analyzes the Olist ecommerce and marketing datasets using **SQLite, SQL, Python, pandas, SciPy, and Matplotlib**.

The analysis focuses on two connected business questions:

1. **Marketing acquisition:** How effectively do marketing-qualified leads (MQLs) convert into closed deals, and which acquisition channels perform best?
2. **Customer retention:** How often do customers return after their first purchase, and is repeat purchasing associated with first-purchase review scores?

The project was built as an end-to-end analytics workflow: CSV data was loaded into SQLite, relational tables and keys were validated, SQL was used to extract and aggregate data, and Python was used for statistical analysis and visualization.

---

## Business Questions

### Marketing funnel
- What is the overall MQL-to-deal conversion rate?
- Which acquisition channels generate the most leads and deals?
- Which channels convert above or below the overall benchmark?
- Are differences in channel conversion statistically meaningful?
- How did 60-day conversion change over time?

### Customer retention
- How many unique customers make a genuine later purchase?
- How long does it take repeat customers to return?
- How does 90-day repeat purchasing vary by customer cohort?
- Is first-purchase review score associated with repeat purchasing?

---

## Data Model

The SQLite database contains the following core tables:

- `marketing_qualified_leads`
- `closed_deals`
- `customers`
- `orders`
- `order_items`
- `order_payments`
- `order_reviews`
- `products`
- `sellers`
- `geolocation`

Primary and foreign key relationships were checked against the project ER diagram before analysis.

A few data-model considerations were important during analysis:

- `customer_unique_id`, rather than `customer_id`, was used to identify customers across multiple orders.
- Geolocation ZIP-code prefixes are not unique in the raw geolocation table and therefore require special treatment rather than being assumed to be a simple unique primary key.
- Multiple review records can exist for a single order, so review scores were aggregated at order level before joining them into retention analysis.

---

## Tools

- **SQLite / DB Browser for SQLite** — database creation and inspection
- **SQL** — joins, aggregation, cohort construction, and funnel metrics
- **Python** — analysis workflow
- **pandas** — data manipulation
- **SciPy** — statistical testing
- **Matplotlib** — visualization
- **Jupyter Notebook** — reproducible analysis

---

# Key Findings

## 1. Overall MQL-to-Deal Conversion

The marketing dataset contains:

- **8,000 MQLs**
- **842 closed deals**
- **10.53% overall MQL-to-deal conversion rate**

This rate was used as the benchmark for evaluating individual acquisition channels.

---

## 2. Acquisition Channel Performance

The major channels showed substantial differences in conversion efficiency.

| Channel | MQLs | Deals | Conversion Rate | Share of Deals |
|---|---:|---:|---:|---:|
| Organic Search | 2,296 | 271 | 11.80% | 32.73% |
| Paid Search | 1,586 | 195 | 12.30% | 23.55% |
| Social | 1,350 | 75 | 5.56% | 9.06% |
| Direct Traffic | 499 | 56 | 11.22% | 6.76% |
| Referral | 284 | 24 | 8.45% | 2.90% |
| Email | 493 | 15 | 3.04% | 1.81% |

`unknown` was analyzed but excluded from actionable channel recommendations because its underlying acquisition source is unclear.

### Interpretation

**Organic search** produced the largest number of closed deals and combined high lead volume with above-average conversion.

**Paid search** also performed strongly, converting at 12.30% compared with the 10.53% overall benchmark.

**Social** generated substantial lead volume but converted at only 5.56%, indicating that lead quantity did not translate proportionally into closed deals.

**Email** had an especially low conversion rate of 3.04%.

---

## 3. Statistical Comparison of Acquisition Channels

Channel conversion rates were compared with the overall conversion benchmark.

Notable results included:

- Paid search: **+1.87 percentage points**, `p = 0.015`
- Organic search: **+1.37 percentage points**, `p = 0.031`
- Social: **−4.87 percentage points**, `p < 0.001`
- Email: **−7.39 percentage points**, `p < 0.001`

These results provide evidence that paid and organic search converted above the benchmark, while social and email converted below it.

Statistical significance was interpreted alongside business impact rather than in isolation.

---

## 4. Conversion Time and 60-Day Cohorts

Among valid converted leads, the median time from first contact to closed deal was approximately **14.7 days**. The distribution was strongly right-skewed, with some conversions taking several months.

One record contained a negative conversion duration of approximately 1.2 days. This record was treated as a data-quality anomaly and excluded from conversion-time calculations rather than modifying the source data.

A standardized **60-day conversion window** was used for cohort comparison.

For 2018 MQL cohorts:

| Cohort | 60-Day Conversion Rate |
|---|---:|
| January 2018 | 10.25% |
| February 2018 | 11.58% |
| March 2018 | 11.41% |
| April 2018 | 11.98% |
| May 2018 | 7.75% |

Conversion remained relatively stable around 10–12% from January through April before falling to **7.75% in May**.

Further channel-level analysis showed that the May decline occurred across several major acquisition channels rather than being explained solely by a shift in channel mix.

---

# Customer Retention

## 5. Repeat Purchasing

The ecommerce data contains **96,096 unique customers**.

An initial order-count analysis identified multiple orders placed by the same customer at exactly the same timestamp. These were treated as part of the same purchasing event rather than evidence that the customer had returned.

After defining a repeat purchase as an order placed at a **strictly later timestamp** than the customer's first purchasing event:

- **2,740 customers** made a genuine later purchase.
- The observed repeat-purchase rate was **2.85%**.

This distinction prevented simultaneous orders from artificially inflating the retention metric.

---

## 6. Time to Repeat Purchase

Among genuine repeat customers:

- Median time to repeat: **39.1 days**
- 75th percentile: **138.3 days**
- 90th percentile: **260.4 days**

Because customers entering the dataset near its end have less opportunity to return, a standardized **90-day repeat-purchase window** was used for cohort analysis.

Across substantial customer cohorts, 90-day repeat rates were generally only **1–3%**.

The main conclusion is therefore not a particular monthly fluctuation, but that short-term repeat purchasing remained consistently low throughout the observed period.

---

## 7. Review Score vs. Repeat Purchasing

First-purchase review score showed little apparent relationship with 90-day repeat purchasing.

| Review Score | Customers | 90-Day Repeat Customers | Repeat Rate |
|---:|---:|---:|---:|
| 1 | 10,214 | 194 | 1.90% |
| 2 | 2,797 | 51 | 1.82% |
| 3 | 7,264 | 149 | 2.05% |
| 4 | 16,785 | 298 | 1.78% |
| 5 | 49,259 | 1,001 | 2.03% |

Repeat rates remained tightly grouped around 1.8–2.1%, with no clear monotonic relationship between higher review scores and higher repeat purchasing.

This analysis identifies an **association**, not causation. The data does not support the claim that review score causes or prevents repeat purchasing.

---

# Business Recommendations

### Prioritize lead quality alongside acquisition volume

Organic and paid search combine relatively strong conversion with meaningful scale. These channels are stronger candidates for continued investment than channels evaluated solely on lead volume.

### Investigate social lead qualification

Social generated 1,350 MQLs but converted at only 5.56%, significantly below the overall benchmark. Rather than simply increasing social lead volume, the business should investigate targeting, campaign quality, and lead qualification.

### Investigate the May 2018 funnel decline

The May cohort experienced a broad decline in 60-day conversion across several acquisition channels. Because channel mix alone does not explain the change, operational or funnel-level factors should be investigated before attributing the decline to marketing source performance.

### Treat retention as a separate strategic challenge

Repeat purchasing remained low across cohorts, and first-purchase review score did not meaningfully explain the pattern. Retention initiatives should therefore investigate factors beyond review satisfaction, such as product purchasing frequency, customer lifecycle, category characteristics, pricing, promotions, and marketplace behavior.

---

# Data Quality and Analytical Decisions

Several issues were identified during analysis:

- One MQL had a `won_date` slightly earlier than its `first_contact_date` and was excluded from conversion-duration calculations.
- Multiple orders could occur for the same customer at exactly the same timestamp. These were treated as one initial purchasing event for retention purposes.
- Some orders had multiple review records. Review scores were aggregated at order level before analysis.
- `unknown` acquisition origin was retained for descriptive analysis but excluded from actionable channel recommendations.
- Very small cohorts were not used to draw business conclusions because percentages based on only a few observations are unstable.

These decisions were made in the analysis rather than altering the underlying source records.

---

# Limitations

- The analysis is observational and does not establish causal relationships.
- The available dataset represents a finite observation period, so lifetime customer retention cannot be measured directly.
- The 60-day conversion and 90-day retention windows are standardized analytical definitions and do not capture every eventual conversion or repeat purchase.
- Acquisition records categorized as `unknown` cannot be reliably attributed to a specific marketing channel.
- The dataset does not contain all possible drivers of conversion and retention, such as marketing spend, campaign targeting, customer acquisition cost, or detailed customer-level behavioral data.

---

# Repository Structure

```text
olist-marketing-analysis/
├── data/
│   └── README.md
├── notebooks/
│   └── marketing_funnel_retention_analysis.ipynb
├── sql/
│   ├── 01_database_validation.sql
│   ├── 02_marketing_funnel.sql
│   └── 03_customer_retention.sql
├── images/
│   ├── channel_performance.png
│   ├── conversion_trend.png
│   └── retention_cohorts.png
├── README.md
├── requirements.txt
└── .gitignore
```

The raw CSV files and SQLite database are not included in the repository. The notebook expects a local SQLite database path to be configured before execution.

---

# Running the Analysis

1. Clone or download the repository.
2. Create the SQLite database from the Olist source CSV files.
3. Configure the database path in the analysis notebook.
4. Install the required Python packages.
5. Open the notebook and use **Restart Kernel → Run All** to reproduce the analysis.

Example dependencies:

```text
pandas
matplotlib
scipy
jupyter
```

SQLite support is included with Python through the standard `sqlite3` library.

---

## Summary

The analysis found that marketing performance varies substantially by acquisition channel: organic and paid search combined scale with above-average conversion, while social generated substantial lead volume but converted poorly. Conversion performance was stable through early 2018 before a broad decline in May.

On the customer side, genuine repeat purchasing was rare and remained low across 90-day cohorts. First-purchase review scores showed little association with repeat purchasing, suggesting that the retention challenge cannot be explained by customer review satisfaction alone.
