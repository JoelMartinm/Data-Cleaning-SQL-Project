
# Layoffs Data Cleaning Project (SQL)

Welcome to my hands-on SQL data cleaning project! This repository highlights a complete workflow to clean, standardize, and prepare a real-world layoffs dataset for analysis—demonstrating practical SQL skills you can apply to any raw dataset.

---

## 🚩 Project Summary

Data, in its raw form, is often riddled with duplicates, inconsistent values, and missing fields. This project guides you step-by-step through how I took a messy layoffs dataset and turned it into a trusted, analysis-ready source using only SQL.

---

## 🔍 Project Workflow

### 1️⃣ Initial Data Review

- Explored the dataset to understand its structure.
- Created a **staging table** to avoid corrupting raw data.
```sql
CREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;
```

---

### 2️⃣ De-duplication

- Detected duplicate rows using SQL window functions.
- Kept only the first occurrence in each duplicate group.
```sql
WITH deduped AS (
  SELECT *,
    ROW_NUMBER() OVER (
      PARTITION BY company, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions
      ORDER BY company
    ) AS rn
  FROM layoffs_staging
)
DELETE FROM layoffs_staging WHERE rn > 1;
```

---

### 3️⃣ Text Cleaning & Standardization

- Removed trailing spaces and punctuation.
- Harmonized industry names (e.g., all forms of "crypto" became "Crypto").
```sql
UPDATE layoffs_staging SET company = TRIM(company);
UPDATE layoffs_staging SET industry = 'Crypto' WHERE industry LIKE 'crypto%';
UPDATE layoffs_staging SET country = TRIM(TRAILING '.' FROM country);
```

---

### 4️⃣ Data Type Conversion

- Converted `date` fields from string to DATE type for easier analysis.
```sql
UPDATE layoffs_staging SET date = STR_TO_DATE(date, '%m/%d/%Y');
ALTER TABLE layoffs_staging MODIFY COLUMN date DATE;
```

---

### 5️⃣ Filling & Filtering Missing Data

- Filled missing industries by joining on company name.
- Deleted records missing critical values (`total_laid_off` and `percentage_laid_off`).
```sql
UPDATE layoffs_staging t1
JOIN layoffs_staging t2 ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE (t1.industry IS NULL OR t1.industry = '') AND t2.industry IS NOT NULL;

DELETE FROM layoffs_staging WHERE total_laid_off IS NULL AND percentage_laid_off IS NULL;
```

---

### 6️⃣ Finalization

- Dropped any temporary columns.
- The result: a clean, consistent, ready-to-analyze dataset!

---

## 🧠 Key Takeaways

- **SQL window functions** are game-changers for data cleaning!
- Consistency in text data is just as vital as handling numbers.
- Proactive null management prevents misleading analysis down the road.

---

## 🖼️ Screenshots

See the [`screenshots/`](screenshots/) folder for step-by-step visuals of the queries and their results.

---

## 🚀 How To Reproduce

1. **Clone the repo**  
   `git clone https://github.com/YOUR_USERNAME/layoffs-data-cleaning-sql.git`
2. **Import the SQL script**  
   Use your favorite SQL tool to run [`DATA CLEANING PROJECT SQL.sql`](./DATA%20CLEANING%20PROJECT%20SQL.sql).
3. **Run each query** step by step and compare your results with the screenshots provided.

---

## 📂 SQL Script

Full workflow in [`DATA CLEANING PROJECT SQL.sql`](./DATA%20CLEANING%20PROJECT%20SQL.sql)

---

*Made with 💻 and a lot of patience for messy data! If you have questions, feedback, or suggestions, feel free to open an issue or a pull request.*

