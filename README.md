# Customer Shopping Behavior Analysis

An end-to-end data analytics project using **Python**, **SQL Server** and **Power BI** to understand how customer demographics, product preferences, discounts, shipping and subscriptions relate to purchasing behavior and revenue.

![Dashboard](image/dashboard.png)

---

## Business Problem

A retail company wants to understand customer shopping behavior to improve sales, customer engagement, loyalty and marketing effectiveness.

**Main question:** How can the company use customer shopping data to identify purchasing trends, improve customer engagement, and optimize marketing and product strategies?

## Tools

| Step | Tool |
|---|---|
| Data cleaning & EDA | Python (pandas, matplotlib) — Jupyter Notebook |
| Database & analysis | Microsoft SQL Server (T-SQL) |
| Data loading | SQLAlchemy + pyodbc |
| Dashboard | Power BI Desktop |

## Dataset

- **Source:** [Customer Shopping Trends Dataset (Kaggle)](https://www.kaggle.com/datasets/iamsouravbanerjee/customer-shopping-trends-dataset)
- **Size:** 3,900 rows × 18 columns (one row per customer purchase)
- **Features:** age, gender, item, category, purchase amount (USD), location, size, color, season, review rating, subscription status, shipping type, discount, promo code, previous purchases, payment method, purchase frequency
- **Note:** The dataset is synthetic and was created for learning purposes.

## Project Workflow

```
Raw CSV  →  Python (clean & transform)  →  SQL Server (business queries)  →  Power BI (dashboard)  →  Insights
```

## Repository Structure

```
├── image/
│   └── dashboard.png                          # dashboard screenshot
├── customer_shopping_behavior.csv             # raw data
├── customer_shopping_behavior_cleaned.csv     # cleaned data (output of the notebook)
├── Customer_Shopping_Behavior.ipynb           # cleaning, feature engineering, EDA, load to SQL Server
├── customer_behavior_analysis.sql             # 10 business questions in T-SQL
├── customer_behavior_dashboard.pbix           # Power BI report
├── requirements.txt
└── README.md
```

---

## 1. Data Cleaning & Preparation (Python)

| Step | What was done |
|---|---|
| Missing values | 37 missing `Review Rating` values filled with the **median rating of each product category** |
| Column names | Converted to lowercase `snake_case`; `purchase_amount_(usd)` → `purchase_amount` |
| Age groups | New `age_group` column (Young_Adult, Adult, Middle_aged, Senior) using **age quartiles** (18–31, 32–44, 45–57, 58–70) so each group has a similar number of customers |
| Purchase frequency | Merged duplicate labels (`Bi-Weekly` = `Fortnightly`, `Every 3 Months` = `Quarterly`) and created `purchase_frequency_days` (7, 14, 30, 90, 365) |
| Redundant column | `promo_code_used` was identical to `discount_applied` in every row → dropped |
| Validation | 0 missing values, 0 duplicate rows; final table 3,900 × 19 |
| Load | Cleaned DataFrame written to SQL Server (`customer_behavior.customer`) with SQLAlchemy |

## 2. Business Analysis (SQL Server)

| # | Question | Result |
|---|---|---|
| 1 | Revenue by gender | Male **$157,890** · Female **$75,191** |
| 2 | Discount users spending at or above the average ($59.76) | **839** of 1,677 discount users (~50%) |
| 3 | Top 5 products by average rating | Gloves 3.86 · Sandals 3.84 · Boots 3.82 · Hat 3.80 · Skirt 3.78 |
| 4 | Average purchase: Standard vs. Express shipping | Express **$60.48** · Standard **$58.46** |
| 5 | Subscribers vs. non-subscribers | Subscribers: 1,053 customers, avg $59.49, $62,645 revenue · Non-subscribers: 2,847 customers, avg $59.87, $170,436 revenue |
| 6 | Products most often bought with a discount | Hat 50.0% · Sneakers 49.7% · Coat 49.1% · Sweater 48.2% · Pants 47.4% |
| 7 | Customer segments by previous purchases | New 83 · Returning 701 · Loyal 3,116 |
| 8 | Top 3 products per category (`RANK`, ties kept) | Accessories: Jewelry, Belt, Sunglasses · Clothing: Blouse, Pants, Shirt · Footwear: Sandals, Shoes, Sneakers · Outerwear: Jacket, Coat |
| 9 | Subscription rate by purchase history | >5 previous purchases: **27.56%** · 5 or fewer: **22.41%** |
| 10 | Revenue by age group | Young_Adult $62,143 · Middle_aged $59,197 · Adult $55,978 · Senior $55,763 |

## 3. Power BI Dashboard

- **KPIs:** Total Customers (3,900) · Total Revenue ($233,081) · Average Purchase Amount ($59.76) · Average Review Rating (3.75)
- **Visuals:** Customer distribution by subscription status · Revenue by gender · Revenue by category · Revenue by age group · Top 10 products by revenue
- **Slicers:** Subscription, Gender, Category, Shipping Type, Payment Method

---

## Key Insights

1. **Spend per purchase is almost the same for every group (~$60).** Gender, age group, subscription status and discount use barely change how much a customer spends per purchase. Differences in total revenue come mainly from **how many customers** are in each group.
2. **Men generate ~68% of revenue because there are more male customers** (2,652 vs. 1,248), not because they spend more. Average purchase: Female $60.25, Male $59.54.
3. **Clothing and Accessories drive 77% of revenue** ($104,264 and $74,200). Outerwear contributes the least ($18,524).
4. **Frequent buyers are more likely to subscribe:** 27.6% of customers with more than 5 previous purchases are subscribers, compared with 22.4% of the others.
5. **Subscribers do not spend more per purchase** ($59.49 vs. $59.87).
6. **The customer base is mostly loyal:** 80% of customers have more than 10 previous purchases, while only 2% are new.
7. **Discounts do not lower basket size:** about half of discount users still spend above the average purchase amount.

## Recommendations

- **Target subscription offers at repeat buyers.** Customers with several purchases already subscribe more often; offer membership after the 3rd–5th purchase and track the subscription rate.
- **Review the value of the subscription program.** Subscribers do not spend more per purchase, so test benefits that increase basket size or purchase frequency (e.g. bundles, early access).
- **Focus on acquiring new customers.** Only 2% of customers are new; acquisition campaigns may have more impact than further loyalty spending.
- **Protect the core categories.** Clothing and Accessories bring most of the revenue; prioritize stock and promotion of their top sellers (Blouse, Shirt, Dress, Pants, Jewelry).
- **Check discount-heavy products.** Hat, Sneakers and Coat are sold with a discount about half of the time; compare their margins with and without discounts before continuing these promotions.
- **Avoid over-segmenting by age or gender** for pricing, since spend per purchase is nearly identical across these groups.

## Data Limitations

- **Discounts and subscriptions appear only for male customers** — no female customer has a discount or subscription. Comparisons between discount/subscription groups are therefore partly a male vs. female comparison and should be interpreted with care.
- The dataset is **synthetic**: purchase amounts are spread almost evenly between $20 and $100, and many differences between groups are very small.
- There is no date or profit information, so trends over time and profitability cannot be analyzed.

---

## How to Run

1. Clone the repository and install the requirements:
   ```bash
   pip install -r requirements.txt
   ```
2. Create a database named `customer_behavior` in SQL Server.
3. Run `Customer_Shopping_Behavior.ipynb` (update `server` in the connection cell if needed). This cleans the data and loads it into the `customer` table.
4. Run the queries in `customer_behavior_analysis.sql` in SQL Server Management Studio.
5. Open `customer_behavior_dashboard.pbix` in Power BI Desktop and refresh the data source.

## My Additions

The overall project structure was inspired by a tutorial (see Acknowledgments). I built it with **SQL Server instead of PostgreSQL** and added the following:

- Merged duplicate frequency labels (Bi-Weekly/Fortnightly, Every 3 Months/Quarterly) before converting them to days
- Quartile-based age groups so each group has a similar number of customers
- An EDA section with charts and a check of how discounts and subscriptions are distributed across genders
- Fixed integer averaging in SQL Server (`AVG` on an integer column returns an integer) in the discount query
- Used `RANK()` so tied products are shown correctly in the top-3-per-category query
- Added a subscription-rate comparison to answer whether repeat buyers are more likely to subscribe
- Data limitations section

## Acknowledgments

This project was built with support from the [WeXllent YouTube channel](https://youtube.com/@c_wexllent) tutorial on customer shopping behavior analysis.

## Author

**Erva Özcan** — [GitHub](https://github.com/ervaozcan)
