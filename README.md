# Drug Labels & Side Effects — SQL Analysis

## 📊 Key Insights

* Between **2000–2015**, antifungal approvals peaked with one manufacturer leading in volume.
* **Oral (Enteral) administration** dominates, but Parenteral (IV/IM) and Topical routes are still significant.
* Manufacturers can be grouped into **Medium, Large, and X-Large categories** by the number of drugs produced.
* Several drugs flagged **pregnancy and fertility risks** in their warnings and contraindications; This might be a safety concerns for people wanting children.

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
