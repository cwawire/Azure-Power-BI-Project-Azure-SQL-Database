# 👕 Men’s T-Shirt Brands — Profitability & Pricing Analysis  
**Pipeline:** Azure Storage ➜ Azure SQL Database (MSSQL) ➜ Power BI  

This project analyzes men’s T-shirt brands to uncover insights on **revenue, discount depth, and profit sensitivity** using a cloud-first data pipeline and visualization in Power BI.

---

## ✅ Project Highlights

- **Dataset Size:** 1,444 SKUs | 144 Brands  
- **Avg Sale Price:** 1,034 (Median 699)  
- **Avg Discount:** 59.6%  
- **Discounted SKUs:** ~89.6%  

**Top Brands by Total Sale (Revenue Proxy):**
1. **ARMANI EXCHANGE** — 139,968  
2. **U.S. Polo Assn.** — 135,409  
3. **The Indian Garage Co** — 93,738  
4. **SUPERDRY** — 75,610  
5. **BROOKS BROTHERS** — 71,520  

---

## 🏗 Architecture

**Azure Blob ➜ Azure SQL (MSSQL) ➜ Power BI Desktop ➜ Power BI Service**

- **Data Cleaning:** Power Query (price normalization, discount calculation)  
- **Modeling:** DAX measures for revenue, profit estimation, and discount analysis  
- **Scenario Analysis:** What-If parameter for cost assumptions (profit sensitivity)  

---

## 📂 Repo Structure

```
.
├─ data/
│  └─ Men+Tshirt.csv
├─ pbix/
│  └─ MensTshirts_ProfitAnalysis.pbix
├─ sql/
│  └─ create_tables.sql
├─ images/
│  ├─ top_brands_total_sale.png
│  └─ top_brands_discount.png
└─ README.md
```

---

## 📊 KPIs & Metrics

- **SKU Count:** 1,444  
- **Brands:** 144  
- **Avg Sale Price:** 1,034  
- **Median Sale Price:** 699  
- **Avg Discount %:** 59.6%  
- **Share Discounted:** 89.6%  

---

## 🔍 Analysis Focus

1. Which brands lead in **total sale (revenue proxy)**?
2. How steep are **average discounts** across brands?
3. Which brands remain profitable under different **cost-to-list % assumptions**?
4. Pricing variance & discount dependency.

---

## 🧱 Data Preparation (Power Query)

```m
let
    Source = Csv.Document(File.Contents("Men+Tshirt.csv"),[Delimiter=",", Encoding=65001, QuoteStyle=QuoteStyle.Csv]),
    Promote = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    Select = Table.SelectColumns(Promote, {"Brand","Title","Original Price","Sale Price"}),
    CleanOriginal = Table.TransformColumns(Select, {{"Original Price", each Number.FromText(Text.Select(Text.From(_), {"0".."9","."})), type number}}),
    CleanSale = Table.TransformColumns(CleanOriginal, {{"Sale Price", each Number.FromText(Text.Select(Text.From(_), {"0".."9","."})), type number}}),
    AddDiscount = Table.AddColumn(CleanSale, "Discount", each [Original Price] - [Sale Price], type number),
    AddDiscountPct = Table.AddColumn(AddDiscount, "Discount %", each if [Original Price] <> null and [Original Price] <> 0 then ([Discount] / [Original Price]) else null, type number)
in
    AddDiscountPct
```

---

## 📐 Key DAX Measures

```DAX
Total Revenue = SUM ( Tshirts[Sale Price] )

Estimated Cost =
SUMX ( Tshirts, Tshirts[Original Price] * SELECTEDVALUE ( 'Cost Ratio'[Cost Ratio] ) )

Estimated Profit = [Total Revenue] - [Estimated Cost]

Profit Margin % = DIVIDE ( [Estimated Profit], [Total Revenue] )

Avg Discount % = AVERAGE ( Tshirts[Discount %] )
```

> Use **What-If parameter** for cost-to-list ratio (e.g., 30%–80%) to model profit scenarios.

---

## 📌 Next Steps

- Add **time series** for trend analysis  
- Include **unit sales** or **cost data** for accurate profit margins  
- Segment by **category/style** for deeper insight  

---
