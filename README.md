# Drug Labels & Side Effects — SQL Analysis

## 📊 Key Insights

* Some manufacturers demonstrate consistently higher approval success rates (e.g., Johnson & Johnson in Antidepressants), while others struggle with higher rejection levels (e.g., GlaxoSmithKline).
* Between **2000–2015**, antifungal approvals peaked with one manufacturer leading in volume.
* **Oral (Enteral) administration** dominates, but Parenteral (IV/IM) and Topical routes are still significant.
* Multiple manufacturers have drugs with pregnancy or fertility warnings. As a managing body, this requires closer scrutiny for compliance and patient safety monitoring.*
* Variations in price and expiry timelines across manufacturers suggest opportunities to optimize supply chains and forecast potential drug shortages.
* X-Large manufacturers (>150 drugs) dominate the portfolio volume, but Medium manufacturers (<135 drugs) may present niche approvals that balance the portfolio risk.

---
## 📈 Interactive Dashboard

To complement the SQL analysis, I built an interactive Tableau dashboard that visualizes key trends across drug approvals, manufacturer performance, drug classes, administration routes, and safety warnings.

🔗 **Explore the dashboard here**: [View on Tableau Public](https://public.tableau.com/views/DrugLabelsSideEffectsSQLAnalysis/Dashboard1?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)


### Dashboard Highlights:

* **Manufacturer Performance**: Visual comparison of approval success rates by drug class.
* **Administration Routes**: Distribution of Enteral, Parenteral, and Topical drugs.
* **Safety Flags**: Identification of drugs with pregnancy or fertility-related warnings.
* **Approval Trends (2000–2015)**: Focused view on antifungal drug approvals over time.

> 🛠️ *Built with Tableau using SQL-extracted data for business and clinical decision support.*


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2dae8d29-d05a-48a3-8efc-893c0d152567" />

---
## 📌 Overview

This project explores the **Drug Labels & Side Effects dataset** from Kaggle. Using SQL, I performed exploratory analysis to answer business and clinical questions around manufacturers, drug classes, approvals, and safety concerns.

The goal is to demonstrate **SQL querying, categorization, aggregation, and data storytelling** for real-world insights.

---

## ⚙️ Dataset

* **Source**: [Drug Labels & Side Effects Dataset](https://www.kaggle.com/datasets/pratyushpuri/drug-labels-and-side-effects-dataset-1400-records/data)
* **Size**: 1,400 rows, multiple attributes (drug name, manufacturer, class, approval year, price, expiry, warnings, contraindications, administration route).
* **Missing Data**: N/A
---


## 🧠 Key Skills Demonstrated

* **SQL Joins, Filtering, Grouping, Aggregations**
* **Case Statements & Conditional Categorization**
* **Window Functions for Percentages**
* **Business-Oriented Data Storytelling**

---

## 🚀 How to Run

1. Clone this repo
2. Load the dataset into a relational database (e.g., PostgreSQL, MySQL, SQLite)
3. Run queries in your SQL editor of choice

---


## ❓ Business & Clinical Questions Answered

### 1. Which drug manufacturers are in the dataset?

```sql
SELECT DISTINCT manufacturer
FROM realistic_drug_labels_side_effects
ORDER BY manufacturer;
```

---

### 2. Top 5 manufacturers with the highest number of antidepressants

```sql
SELECT manufacturer,
       COUNT(drug_name) AS antidepressant_count
FROM realistic_drug_labels_side_effects
WHERE drug_class = 'Antidepressant'
GROUP BY manufacturer
ORDER BY antidepressant_count DESC
LIMIT 5;
```

---

### 3. Which drug & manufacturer had the highest number of antifungal approvals between 2000–2015?

```sql
SELECT drug_name,
       manufacturer,
       COUNT(drug_name) AS antifungal_count,
       approval_year
FROM realistic_drug_labels_side_effects
WHERE drug_class = 'Antifungal'
  AND approval_year BETWEEN 2000 AND 2015
GROUP BY drug_name, manufacturer, approval_year
ORDER BY antifungal_count DESC
LIMIT 1;
```

---

### 4. How are drugs distributed by administration route (Enteral, Parenteral, Topical/Mucosal)?

```sql
SELECT administration_route_classification,
       COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS percentage
FROM (
    SELECT drug_name,
           CASE 
               WHEN administration_route IN ('Rectal','Sublingual','Oral') 
                    THEN 'Enteral'
               WHEN administration_route IN ('Inhalation','Topical') 
                    THEN 'Topical/Mucosal'
               WHEN administration_route IN ('Intravenous','Intramuscular') 
                    THEN 'Parenteral'
               ELSE 'Other' 
           END AS administration_route_classification
    FROM realistic_drug_labels_side_effects
) t
GROUP BY administration_route_classification
ORDER BY percentage DESC;
```

---

### 5. How can manufacturers be grouped into size categories (Medium, Large, X-Large)?

```sql
SELECT manufacturer,
       COUNT(drug_name) AS total_drugs,
       CASE 
           WHEN COUNT(drug_name) < 135 THEN 'Medium'
           WHEN COUNT(drug_name) BETWEEN 136 AND 150 THEN 'Large'
           WHEN COUNT(drug_name) > 150 THEN 'X-Large'
       END AS manufacturer_size
FROM realistic_drug_labels_side_effects
GROUP BY manufacturer
ORDER BY total_drugs DESC;
```

---

### 6. Which drugs list pregnancy or fertility warnings/contraindications?

```sql
SELECT drug_name, contraindications, warnings, price_usd, expiry_date, approval_status
FROM realistic_drug_labels_side_effects
WHERE contraindications LIKE '%Pregnancy%' OR warnings LIKE '%fertility%'
ORDER BY approval_status;
```

---
### 7. Which drugs list pregnancy or fertility warnings/contraindications?

```sql
SELECT 
    manufacturer,
    drug_class,
    approval_status,
    COUNT(drug_name) AS total_drugs
FROM realistic_drug_labels_side_effects
GROUP BY manufacturer, drug_class, approval_status
ORDER BY manufacturer, drug_class, approval_status;
```
---
