---
title: "Chaos to Clarity, using MySQL"
datePublished: 2026-03-10T08:45:08.018Z
cuid: cmmkd712p01962boc9zed4jfu
slug: chaos-to-clarity-using-mysql
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ac37e13d-e9cb-493a-82d6-e2a79e13da16.png

---

Data is rarely handed to you on a silver platter. Most of the time, it arrives messy, inconsistent, and full of "noise." Recently, I took on the challenge of cleaning and standardizing a comprehensive dataset on global layoffs, using MySQL.

Basically one of my first times managing an entire end-to-end data cleaning process, and I found it quite interesting and rewarding. There’s a unique satisfaction in taking a "broken" dataset and transforming it into a structured and reliable output.

Here is the step-by-step workflow I used to ensure the data was ready for analysis.

**The Problem**

The raw layoffs data was plagued with inconsistencies:

*   Duplicate entries.
    
*   Inconsistent naming (e.g., "Crypto," "Crypto Currency," and "Cryptocurrency").
    
*   Missing values and incorrect data types (dates stored as text).
    

**Safety First: The Staging Table**

Before touching the data, I followed a professional best practice: **Never modify raw data.** I created a "Staging Table" to act as my playground.

> `-- Creating a staging table to preserve the raw data`
> 
> `CREATE TABLE layoffs_staging1`
> 
> `LIKE layoffs;`
> 
> `INSERT INTO layoffs_staging1`
> 
> `SELECT *`
> 
> `FROM layoffs;`

**The Hunt for Duplicates**

Since the dataset lacked a unique primary key, I had to get creative. I used the **Window Function (ROW\_NUMBER)** to partition the data across all columns. If a row appeared more than once, it was flagged for removal.

> `-- Identifying duplicates by assigning row numbers to identical records`
> 
> `SELECT *,`
> 
> `ROW_NUMBER() OVER(`
> 
>     `PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions`
> 
> `) AS row_num`
> 
> `FROM layoffs_staging1;`

*Note: I moved this into a second staging table (layoffs\_staging2) to safely DELETE any record where row\_num > 1.*

**Standardizing the "Noise"**

This was my favorite part: making the data consistent.

*   **Company Names:** Used TRIM() to remove accidental white spaces that mess up grouping.
    
*   **Industry Syncing:** I noticed the "Crypto" industry had three different names. I standardized them into one clear category.
    

> `-- Merging variations like 'Crypto Currency' into a single 'Crypto' tag`
> 
> `UPDATE layoffs_staging2`
> 
> `SET industry = 'Crypto'`
> 
> `WHERE industry LIKE '%crypto%';`

**Fixing the Timeline (Date Conversion)**

The dates were stored as strings (text). To perform any time-series analysis later, I had to convert them into a proper DATE format.

> `-- Converting strings to DATE format and modifying the column type`
> 
> `UPDATE layoffs_staging2`
> 
> `SET 'date' = STR_TO_DATE (date, '%m/%d/%Y');`
> 
> `ALTER TABLE layoffs_staging2`
> 
> `MODIFY COLUMN 'date' DATE;`

**Intelligent Data Recovery**

Instead of just deleting rows with missing industry values, I used "Self-Joins." If a company like Airbnb had the industry listed in one row but missing in another, I used a JOIN to fill in the blanks.

> `-- Populating NULL industries by matching them with existing data for the same company`
> 
> `UPDATE layoffs_staging2 t1`
> 
> `JOIN layoffs_staging2 t2 ON t1.company = t2.company`
> 
> `SET t1.industry = t2.industry`
> 
> `WHERE t1.industry IS NULL AND t2.industry IS NOT NULL;`

**The Final Polish**

Lastly, I removed records that were completely empty (where both total\_laid\_off and percentage\_laid\_off were NULL) as they provided no analytical value.

**Reflections & Lessons Learned**

Completing this project felt like solving a puzzle. What I enjoyed most wasn't just the coding, but the ability to **break down a mess into a clear, documented path** that others can follow.

**Key Takeaways:**

*   **Workflow over Speed:** Staging tables are a lifesaver.
    
*   **Data Integrity:** Cleaning isn't just about deleting; it's about recovering information wherever possible.
    
*   **The Power of SQL:** Window functions and Joins are the "Swiss Army Knife" of any Data Analyst.
    

The dataset is now clean, standardized, and ready for the next phase: **Exploratory Data Analysis (EDA).**