

---

# Drug Labels & Side Effects — SQL Analysis

## 📊 Key Insights

* Certain manufacturers show consistently higher approval success rates (e.g., Johnson & Johnson in Antidepressants), while others struggle with more rejections (e.g., GlaxoSmithKline).
* Between **2000–2015**, antifungal approvals peaked, with one manufacturer leading in volume.
* **Oral (Enteral) administration** dominates, though Parenteral (IV/IM) and Topical routes remain significant.
* Several manufacturers have drugs with pregnancy or fertility warnings — an area requiring close regulatory and safety oversight.
* Price and expiry variations across manufacturers highlight opportunities for supply chain optimization and shortage forecasting.
* X-Large manufacturers (>150 drugs) dominate portfolio volume, while Medium manufacturers (<135 drugs) provide niche approvals that diversify risk.

---

## 📈 📊 Interactive Dashboards (Tableau & Power BI)

To complement the SQL analysis, I built **interactive dashboards** in both **Tableau** and **Power BI**, visualizing trends in approvals, manufacturers, administration routes, and safety flags.

### 🔗 Tableau (Public, Interactive)

[View Dashboard on Tableau Public](https://public.tableau.com/views/DrugLabelsSideEffectsSQLAnalysis/Dashboard1?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

<img width="1920" height="1080" alt="Tableau Dashboard" src="https://github.com/user-attachments/assets/2dae8d29-d05a-48a3-8efc-893c0d152567" />  

### 🖼️ Power BI (Screenshot)

*(Due to licensing, the Power BI dashboard cannot be shared interactively. Screenshot shown below for demonstration.)*

<img width="1654" height="991" alt="power BI screenshot" src="https://github.com/user-attachments/assets/f05f677a-53ee-417a-9a05-0999f8ca45eb" />


**Dashboard Highlights (both versions):**

* Manufacturer performance by approval success rate and drug class
* Distribution of administration routes (Enteral, Parenteral, Topical)
* Safety alerts (pregnancy & fertility warnings)
* Approval trends (2000–2015 antifungal focus)

> 🛠️ Built with **SQL + Tableau/Power BI** for business and clinical decision support

---

## 📌 Project Overview

This project analyzes the **Drug Labels & Side Effects dataset** from Kaggle. Using SQL, I answered real-world business and clinical questions about manufacturers, drug classes, approvals, and safety concerns — and then visualized the findings in Tableau and Power BI.

Goal: demonstrate **SQL querying, data categorization, aggregation, and business-oriented storytelling**.

---

## ⚙️ Dataset

* **Source**: [Drug Labels & Side Effects Dataset](https://www.kaggle.com/datasets/pratyushpuri/drug-labels-and-side-effects-dataset-1400-records/data)
* **Size**: \~1,400 rows (drug name, manufacturer, class, approval year, price, expiry, warnings, contraindications, route)
* **Missing Data**: Not significant

---

## 🧠 Skills Demonstrated

* SQL (Joins, Filtering, Grouping, Aggregations)
* Case statements & conditional categorization
* Window functions for percentages
* Data storytelling with Tableau & Power BI

---

## 🚀 How to Run

1. Clone this repo
2. Load dataset into a relational database (PostgreSQL, MySQL, or SQLite)
3. Run the SQL queries provided

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

### 7. Manufacturer & drug class distribution by approval status

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

