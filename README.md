# 🛒 BlinkIT Sales Analysis | Power BI Dashboard

An interactive 2-page Power BI dashboard that turns **8,523 raw grocery transactions** into a clear view of revenue, customer rating and average sale value, sliceable by item type, outlet type, location tier and fat content.

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-blue)
![Power Query](https://img.shields.io/badge/Power%20Query-Data%20Cleaning-green)
![Excel](https://img.shields.io/badge/Source-Excel-217346?logo=microsoftexcel&logoColor=white)

---

## 📌 Table of Contents
1. [Project Overview](#-project-overview)
2. [Business Questions](#-business-questions)
3. [Dataset](#-dataset)
4. [Data Cleaning (Power Query)](#-data-cleaning-power-query)
5. [DAX Measures](#-dax-measures)
6. [Dashboard Pages](#-dashboard-pages)
7. [Key Insights](#-key-insights)
8. [Recommendations](#-recommendations)
9. [Repository Structure](#-repository-structure)
10. [How to Use](#-how-to-use)
11. [Tools & Skills](#-tools--skills)
12. [Author](#-author)

---

## 🎯 Project Overview

BlinkIT is a quick-commerce grocery business. This project gives category and outlet teams a single Power BI report that answers *what is selling, where, and how well*, built on a cleaned data model with reusable DAX measures.

**Headline numbers**

| Total Sales | Average Rating | Average Sale Value | Items Analysed |
|:-:|:-:|:-:|:-:|
| **1.20M** | **3.92** | **140.99** | **8,523** |

---

## ❓ Business Questions

1. Which item categories and outlet types generate the most revenue?
2. Does average sale value differ by fat content or by store tier?
3. How has revenue evolved by the year an outlet was established?
4. Where is the raw data unreliable, and does cleaning it change the answer?

---

## 📂 Dataset

**File:** `BlinkIT_Grocery_Data.xlsx` (single sheet, 8,523 rows × 12 columns)

| Column | Description |
|---|---|
| Item Fat Content | Low Fat / Regular (5 messy labels in raw data) |
| Item Identifier | Unique product ID |
| Item Type | Product category (16 categories, e.g. Fruits and Vegetables, Snack Foods, Dairy) |
| Outlet Establishment Year | Year the outlet opened (2011–2022) |
| Outlet Identifier | Unique outlet ID |
| Outlet Location Type | Tier 1 / Tier 2 / Tier 3 |
| Outlet Size | Small / Medium / High |
| Outlet Type | Grocery Store, Supermarket Type1 / Type2 / Type3 |
| Item Visibility | Share of display area allotted to the item |
| Item Weight | Weight of the item |
| Sales | Sales value of the item |
| Rating | Customer rating |

**Data quality observations**

| Issue | Count | Handling |
|---|---|---|
| Inconsistent `Item Fat Content` labels (`LF`, `low fat`, `reg`) | 545 rows | Standardised to 2 categories |
| Blank `Item Weight` | 1,463 rows (17.2%) | Left blank, not guessed; SUM-based measures ignore blanks |
| `Item Visibility` recorded as 0 | 526 rows (6.2%) | Kept and flagged as likely placeholders |

---

## 🧹 Data Cleaning (Power Query)

Steps applied in order before loading to the model:

1. **Promote headers & set data types** (Sales, Item Weight, Item Visibility, Rating → decimal; Outlet Establishment Year → whole number)
2. **Trim & clean text** on `Item Fat Content`
3. **Standardise categories**: `LF` (316) and `low fat` (112) → **Low Fat**; `reg` (117) → **Regular**. 5 labels become 2
4. **Flag missing Item Weight** (1,463 rows) instead of imputing
5. **Flag zero Item Visibility** (526 rows)
6. **Close & load** into the data model as `BlinkIT Grocery Data`

---

## 🧮 DAX Measures

Stored in the `Dax_Measure` table:

```DAX
Total_Sales    = SUM('BlinkIT Grocery Data'[Sales])
Average_Sales  = AVERAGE('BlinkIT Grocery Data'[Sales])
Average_Rating = AVERAGE('BlinkIT Grocery Data'[Rating])
```

**`Metrics` field parameter**: a calculated table that lets a single slicer switch the Home-page area chart between `Average_Rating`, `Average_Sales` and `Total_Sales`, so one visual does the job of three.

---

## 📊 Dashboard Pages

The report has **2 pages**, navigated with a Home / Matrix button menu.

### 🏠 Home
- **KPI cards:** Total_Sales, Average_Rating, Average_Sales
- **Slicers:** Outlet Location Type, Outlet Type, Item Type, plus the `Metrics` parameter
- **Funnel chart:** Average_Sales by Item Fat Content
- **Area chart:** Total_Sales by Outlet Establishment Year
- **Bar chart:** Total_Sales by Item Type
- **Donut chart:** Total_Sales share by Item Fat Content
- **Combo chart:** Total_Sales and item count by Outlet Location Type

### 🧾 Matrix
- Same KPI card row and slicers
- **Rows:** Outlet Location Type · **Columns:** Item Fat Content
- **Values:** Sum of Item Weight and Average_Sales
- Grand total: 90,774.97 weight units · Average_Sales 140.99

| Outlet Tier | Low Fat Avg Sales | Regular Avg Sales | Total Avg Sales |
|---|:-:|:-:|:-:|
| Tier 1 | 139.64 | 143.10 | 140.87 |
| Tier 2 | 140.67 | 142.10 | 141.17 |
| Tier 3 | 141.52 | 139.87 | 140.94 |
| **Total** | **140.71** | **141.50** | **140.99** |

### Screenshots
> Add exported screenshots of the report to an `images/` folder and they will show here.

![Home Page](images/home_page.png)
![Matrix Page](images/matrix_page.png)

---

## 💡 Key Insights

1. **Two categories carry outsized weight.** Fruits & Vegetables (~₹1.78L) and Snack Foods (~₹1.75L) together make up about 29% of revenue, while Seafood trails at ~₹9.1K, roughly a 20x gap.
2. **Outlet format matters more than location tier.** Supermarket Type1 drives ~66% of revenue (~₹7.88L from 5,577 items) versus ₹1.52L from 1,083 items for Grocery Stores.
3. **Fat content barely changes order value, but volume differs.** Regular averages 141.50 vs Low Fat 140.71 (under 1% apart), yet Low Fat accounts for **64.6%** of revenue (776.32K) because it is sold far more often.
4. **2018 was a breakout year for outlet openings.** Outlets established in 2018 contribute ~204.5K in sales, about 55% above every other vintage year (~130K each).
5. **Tier 3 outlets lead on total sales** (~472K), ahead of Tier 2 (~393K) and Tier 1 (~336K), largely because they hold more items (3,350 vs 2,785 and 2,388).

---

## ✅ Recommendations

- Investigate the **2018 outlet cohort**: more outlets, bigger stores, or a genuine lift?
- Prioritise the **Supermarket Type1** format in expansion plans
- Resolve the **1,463 blank Item Weight** rows before shipping any weight-based analysis
- Fix **Item Fat Content at the source system** so `LF` / `reg` variants stop recurring on refresh

---

## 🗂 Repository Structure

```
BlinkIT-Sales-Analysis/
│
├── BLINKIT_Sales_Analysis.pbix               # Power BI project (main deliverable)
├── BlinkIT_Grocery_Data.xlsx                 # Source dataset
├── BLINKIT_Sales_Analysis.pdf                # Exported dashboard (2 pages)
├── BlinkIT_Sales_Analysis_Presentation.pptx  # Project walkthrough deck
├── images/                                   # Dashboard screenshots (optional)
└── README.md
```

---

## ▶️ How to Use

1. Clone or download this repository
2. Open `BLINKIT_Sales_Analysis.pbix` in **Power BI Desktop**
3. If prompted, point the data source to your local copy of `BlinkIT_Grocery_Data.xlsx` (*Home → Transform data → Data source settings*)
4. Click **Refresh** and explore the **Home** and **Matrix** pages using the slicers
5. No Power BI Desktop? View `BLINKIT_Sales_Analysis.pdf` for a static copy

---

## 🛠 Tools & Skills

- **Power BI Desktop**: data modelling, report design, field parameters
- **Power Query**: data cleaning and transformation
- **DAX**: calculated measures
- **Microsoft Excel**: source data
- **Skills demonstrated:** data cleaning, KPI design, dashboard storytelling, insight generation

---

## 👤 Author

**Sudeepth Sasikumar**
B.Tech Computer Science | Aspiring Data Analyst
📧 sudeepth203@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/your-profile) · [GitHub](https://github.com/your-username)

⭐ If you found this project useful, consider giving it a star!
