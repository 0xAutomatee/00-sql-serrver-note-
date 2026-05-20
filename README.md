Below is the **outline of all core concepts used** in your Step 07 query.

---

# STEP 07 Query Concepts Outline

## 1. Script Header Comment

```sql
/*==============================================================
...
==============================================================*/
```

### Concepts used:

1. Multi-line comment
2. Documentation inside SQL
3. Fix history
4. Business logic notes
5. Human-readable explanation
6. Helps future debugging
7. Helps remember what changed

---

# 2. Drop Old Table

```sql
DROP TABLE IF EXISTS dbo.step_07_final;
GO
```

### Concepts used:

1. Delete old output table
2. `IF EXISTS` prevents error
3. `dbo` schema
4. `GO` batch separator
5. Re-runnable script
6. Fresh result every run

---

# 3. Variables Declaration

```sql
DECLARE 
    @header_cols NVARCHAR(MAX),
    @month_cols NVARCHAR(MAX),
    ...
    @sql NVARCHAR(MAX);
```

### Concepts used:

1. SQL variables
2. `DECLARE`
3. `NVARCHAR(MAX)`
4. Store dynamic SQL text
5. Store generated column lists
6. Store formulas as text
7. Build query step by step
8. Dynamic pivot logic

---

# 4. Dynamic SQL Concept

Your query is **dynamic SQL**.

Meaning:

```sql
SET @sql = 'SELECT ...';
EXEC sp_executesql @sql;
```

### Concepts used:

1. SQL query stored as text
2. Query generated at runtime
3. Dynamic columns
4. Dynamic invoice names
5. Dynamic formulas
6. `PRINT @sql` for debugging
7. `EXEC sp_executesql` to run generated SQL

---

# 5. Dynamic Invoice Columns

Example:

```sql
SELECT @header_cols =
STRING_AGG(
    '''' + REPLACE(i.ref,'''','''''') + ''' AS ' + QUOTENAME(i.ref),
    ','
)
```

### Concepts used:

1. `STRING_AGG`
2. Combine many rows into one text string
3. Create columns dynamically
4. Invoice numbers become column names
5. `QUOTENAME`
6. Safe column names
7. `REPLACE`
8. Escape single quotes
9. Text concatenation using `+`
10. Dynamic alias creation

---

# 6. `STRING_AGG`

```sql
STRING_AGG(value, ',')
```

### Meaning:

It joins multiple rows into one string.

Example result:

```sql
[IN#5751794], [IN#5751970], [IN#5758097]
```

### Concepts used:

1. Row-to-text conversion
2. Dynamic column generation
3. Comma-separated SQL text
4. Used for invoice columns
5. Used for formulas
6. Used for SUM formulas

---

# 7. `QUOTENAME`

```sql
QUOTENAME(i.ref)
```

### Meaning:

Makes invoice number safe as a column name.

Example:

```sql
IN#5751794
```

becomes:

```sql
[IN#5751794]
```

### Concepts used:

1. Safe SQL column name
2. Handles special characters
3. Handles `#`
4. Avoids syntax errors
5. Important for dynamic pivot

---

# 8. `REPLACE` for Quotes

```sql
REPLACE(i.ref,'''','''''')
```

### Meaning:

Escapes single quote inside dynamic SQL.

### Concepts used:

1. String cleaning
2. Prevent SQL breaking
3. Dynamic SQL safety
4. Quote escaping

---

# 9. `LTRIM(RTRIM())`

```sql
LTRIM(RTRIM(i.ref))
```

### Meaning:

Removes spaces from left and right.

### Concepts used:

1. Clean matching
2. Avoid mismatch because of extra spaces
3. Used in joins
4. Used in WHERE
5. Used in GROUP BY
6. Used in subqueries

---

# 10. Common Invoice Logic

```sql
INNER JOIN
(
    SELECT DISTINCT
        LTRIM(RTRIM(invoice_number)) AS invoice_number
    FROM dbo.step_02_po_details
    WHERE invoice_number IS NOT NULL
) p
ON LTRIM(RTRIM(i.ref)) = p.invoice_number;
```

### Meaning:

Only invoices that exist in both tables are used.

### Concepts used:

1. `INNER JOIN`
2. Subquery
3. `SELECT DISTINCT`
4. Remove duplicate invoices
5. Match invoice table with PO table
6. Only common records
7. Filter NULL values
8. Dynamic report columns

---

# 11. Header Row Logic

```sql
'IN#5751794' AS [IN#5751794]
```

### Meaning:

Column header shows invoice number.

### Concepts used:

1. Static label row
2. Text value inside same column
3. Report formatting
4. Dynamic column name
5. Dynamic column value

---

# 12. Month Row Logic

```sql
FORMAT(TRY_CONVERT(date, i.Ndatee), 'MMM-yy')
```

### Meaning:

Convert invoice date into month format.

Example:

```sql
2026-03-15
```

becomes:

```sql
Mar-26
```

### Concepts used:

1. `TRY_CONVERT`
2. Text-to-date conversion
3. `FORMAT`
4. Month display
5. Safe date conversion
6. Avoid error if date is bad
7. Report header formatting

---

# 13. `TRY_CONVERT`

```sql
TRY_CONVERT(date, i.Ndatee)
```

### Meaning:

Try to convert text into date.

If invalid, returns `NULL`.

### Concepts used:

1. Safe conversion
2. Prevent SQL error
3. Useful when date is stored as text
4. Used before `FORMAT`

---

# 14. `FORMAT`

```sql
FORMAT(date_value, 'MMM-yy')
```

### Meaning:

Changes date display style.

### Concepts used:

1. Date formatting
2. Month-year output
3. Excel-style report display

---

# 15. CR Invoice Value Logic

```sql
SUM
(
    CASE
        WHEN LTRIM(RTRIM(p.invoice_number)) = 'IN#5751794'
        THEN ISNULL(TRY_CAST(p.qxc AS decimal(18,2)),0)
        ELSE 0
    END
)
```

### Meaning:

For each CR row and invoice column, sum `qxc`.

### Concepts used:

1. `SUM`
2. `CASE WHEN`
3. Conditional aggregation
4. Invoice-wise value
5. CR-wise value
6. `TRY_CAST`
7. `ISNULL`
8. Numeric conversion
9. Dynamic pivot logic

---

# 16. `CASE WHEN`

```sql
CASE
    WHEN condition THEN value
    ELSE 0
END
```

### Meaning:

If condition matches, use value. Otherwise use 0.

### Concepts used:

1. If/else logic in SQL
2. Conditional value
3. Used inside SUM
4. Used for pivot-style calculation

---

# 17. Conditional SUM

```sql
SUM(CASE WHEN invoice_number = 'IN#...' THEN qxc ELSE 0 END)
```

### Meaning:

Sum only rows matching that invoice.

### Concepts used:

1. Pivot without PIVOT keyword
2. Invoice column calculation
3. Grouped total
4. Dynamic report cell value

---

# 18. `TRY_CAST`

```sql
TRY_CAST(p.qxc AS decimal(18,2))
```

### Meaning:

Convert text/number into decimal safely.

### Concepts used:

1. Safe numeric conversion
2. Avoid error on bad values
3. Decimal precision
4. Money calculation

---

# 19. `decimal(18,2)`

```sql
decimal(18,2)
```

### Meaning:

Number with 2 decimal places.

Example:

```sql
1234567890123456.99
```

### Concepts used:

1. Money format
2. Numeric precision
3. Accurate SUM
4. Avoid text calculation

---

# 20. `ISNULL`

```sql
ISNULL(value, 0)
```

### Meaning:

If value is NULL, use 0.

### Concepts used:

1. Null handling
2. Prevent blank totals
3. Safe math
4. Avoid NULL result in formulas

---

# 21. Cast Numeric to Text

```sql
CAST(value AS varchar(50))
```

### Meaning:

Convert number into text.

### Why used?

Because all `UNION ALL` rows must have same column data type.

### Concepts used:

1. Data type alignment
2. Report output formatting
3. Avoid UNION type conflict
4. Convert money values to display text

---

# 22. Credit Amount Applied

```sql
@cr_total_formula
```

### Meaning:

Total amount applied for each CR row.

It adds all invoice column values.

### Concepts used:

1. Dynamic formula
2. Sum across invoice columns
3. CR-level total
4. `STRING_AGG` with `+`
5. Conditional aggregation

---

# 23. YCS Row

```sql
YCS Invoice+DB Amount
```

### Meaning:

Shows invoice-wise amount from PO/details table.

### Concepts used:

1. Summary row
2. Invoice total row
3. Uses same invoice dynamic columns
4. Uses `SUM(qxc)`
5. Business comparison row

---

# 24. Difference Row

```sql
Difference = YCS - Total Credit Applied
```

### Concepts used:

1. Difference calculation
2. Invoice-wise comparison
3. Subquery for YCS amount
4. Subquery for applied amount
5. Subtraction
6. NULL-safe math
7. Dynamic column formulas

---

# 25. Subquery Concept

Example:

```sql
SELECT SUM(TRY_CAST(p1.qxc AS decimal(18,2)))
FROM dbo.step_02_po_details p1
WHERE invoice_number = 'IN#...'
```

### Meaning:

A query inside another query.

### Concepts used:

1. Inner calculation
2. Per-invoice value
3. Used inside SELECT
4. Used inside dynamic columns
5. Used for totals

---

# 26. Common Credit CTE

```sql
;WITH common_credit AS
(
    SELECT DISTINCT
        LTRIM(RTRIM(c.ref)) AS ref
    FROM dbo.step_01a_cr_sheet c
    INNER JOIN dbo.step_03_payments_combined p
        ON LTRIM(RTRIM(c.ref)) = LTRIM(RTRIM(p.cr_issue))
)
```

### Meaning:

Only CR# that exists in both CR sheet and payment table.

### Concepts used:

1. CTE
2. `WITH`
3. Common data set
4. Common CR filter
5. Removes unmatched CR
6. Reusable query block
7. `INNER JOIN`
8. `DISTINCT`

---

# 27. Common Invoice CTE

```sql
common_invoice AS
(
    SELECT DISTINCT
        LTRIM(RTRIM(i.ref)) AS ref
    FROM dbo.step_01a_in_sheet i
    INNER JOIN dbo.step_02_po_details p
        ON LTRIM(RTRIM(i.ref)) = LTRIM(RTRIM(p.invoice_number))
)
```

### Meaning:

Only invoices found in both invoice sheet and PO details.

### Concepts used:

1. Common invoice filter
2. Dynamic column control
3. Avoid extra invoices
4. Avoid empty columns

---

# 28. CTE Concept

```sql
;WITH name AS (...)
SELECT ...
```

### Meaning:

Temporary named result used inside same query.

### Concepts used:

1. Temporary logic block
2. Clean readable query
3. Reusable inside final SELECT
4. Helps avoid repeated joins

---

# 29. `UNION ALL`

```sql
SELECT ...
UNION ALL
SELECT ...
UNION ALL
SELECT ...
```

### Meaning:

Stack multiple rows together.

### Your report rows:

1. Header row
2. Month row
3. CR data rows
4. Total Credit Amount row
5. YCS row
6. Difference row
7. Unpaid row
8. Net Difference row

### Concepts used:

1. Combine many SELECT results
2. Create report layout
3. Keep duplicate rows
4. All SELECTs must have same columns
5. All SELECTs must have compatible data types

---

# 30. `SELECT INTO`

```sql
SELECT *
INTO dbo.step_07_final
FROM (...)
```

### Meaning:

Create new table from query result.

### Concepts used:

1. Create table automatically
2. Save final report
3. Output table generation
4. No need `CREATE TABLE`
5. Column names come from SELECT

---

# 31. Blank Columns

```sql
NULL AS [Blank_1]
```

### Meaning:

Adds empty space columns in report.

### Concepts used:

1. Report formatting
2. Excel-like layout
3. Visual separation
4. Fixed column placement

---

# 32. CR Data Rows

```sql
FROM dbo.step_01a_cr_sheet c
INNER JOIN common_credit cc
ON LTRIM(RTRIM(c.ref)) = cc.ref
LEFT JOIN dbo.step_02_po_details p
ON LTRIM(RTRIM(p.cr_issue)) = LTRIM(RTRIM(c.ref))
```

### Meaning:

Start from CR sheet, only common credits, then bring PO values.

### Concepts used:

1. Main CR table
2. `INNER JOIN` for valid CR
3. `LEFT JOIN` for optional PO records
4. Group by CR
5. Calculate invoice values per CR

---

# 33. `INNER JOIN`

```sql
INNER JOIN table2
ON table1.col = table2.col
```

### Meaning:

Only matching rows.

### Used for:

1. Common credits
2. Common invoices
3. Valid matching only
4. Filtering report

---

# 34. `LEFT JOIN`

```sql
LEFT JOIN dbo.step_02_po_details p
ON ...
```

### Meaning:

Keep all left table rows, even if no match on right table.

### Used for:

1. Keep CR row
2. Attach PO details if available
3. Avoid losing CR records
4. Then HAVING removes zero rows

---

# 35. `GROUP BY`

```sql
GROUP BY c.ref, c.pay_day
```

### Meaning:

One output row per CR#.

### Concepts used:

1. Group rows
2. Aggregate invoice amounts
3. Sum PO rows per CR
4. Required when using `SUM`

---

# 36. `HAVING`

```sql
HAVING (' + @cr_total_formula + ') <> 0
```

### Meaning:

After grouping, remove CR rows where total applied is 0.

### Concepts used:

1. Filter after aggregation
2. Different from `WHERE`
3. Remove empty CR rows
4. Uses aggregate formula

---

# 37. `WHERE` vs `HAVING`

## `WHERE`

Filters before grouping.

```sql
WHERE invoice_number IS NOT NULL
```

## `HAVING`

Filters after grouping.

```sql
HAVING SUM(qxc) <> 0
```

---

# 38. Credit Amount As Per A/R Report

```sql
ABS(ISNULL(TRY_CAST(c.pay_day AS decimal(18,2)),0))
```

### Meaning:

Take CR amount from CR sheet.

### Concepts used:

1. `pay_day`
2. Convert to decimal
3. Replace NULL with 0
4. Use `ABS`
5. Make negative credit positive

---

# 39. `ABS`

```sql
ABS(value)
```

### Meaning:

Make number positive.

Example:

```sql
ABS(-100) = 100
```

### Used for:

1. Credit amount
2. Unapplied amount
3. Difference logic
4. Avoid negative sign issue

---

# 40. Difference Per CR

```sql
ABS(c.pay_day) - Credit Amount Applied
```

### Meaning:

CR report amount minus applied amount.

### Concepts used:

1. Business difference
2. Credit reconciliation
3. Per-CR comparison
4. Dynamic total formula

---

# 41. Deductions Per CR

```sql
SELECT SUM(TRY_CAST(pay.total AS decimal(18,2)))
FROM dbo.step_03_payments_combined pay
WHERE pay.cr_issue = c.ref
AND pay.line_type IN ('Returns', 'Adjustments')
```

### Meaning:

For each CR#, sum payment totals where line type is Returns or Adjustments.

### Concepts used:

1. Correlated subquery
2. CR-wise deduction
3. `IN`
4. Filter by line type
5. SUM total
6. Payment table logic

---

# 42. `IN`

```sql
line_type IN ('Returns', 'Adjustments')
```

### Meaning:

Line type must be one of these values.

### Concepts used:

1. Multiple condition filter
2. Cleaner than many ORs
3. Business filter

---

# 43. Correlated Subquery

```sql
WHERE pay.cr_issue = c.ref
```

### Meaning:

Inner query uses value from outer query.

### Concepts used:

1. Per-row calculation
2. Each CR gets its own deduction
3. Each CR gets its own unapplied
4. Dynamic row-specific value

---

# 44. Unapplied Per CR

```sql
SELECT MAX(TRY_CAST(m.sumByCr_unapplied AS decimal(18,2)))
FROM dbo.step_04b_order_not_matched m
WHERE m.cr_issue = c.ref
```

### Meaning:

For each CR#, pick unapplied value from not-matched table.

### Concepts used:

1. Unmatched data
2. CR-wise unapplied value
3. `MAX`
4. Avoid duplicate repeated value
5. Lookup value

---

# 45. Why `MAX` Used

```sql
MAX(sumByCr_unapplied)
```

### Meaning:

If same CR appears multiple times, pick one value.

Usually because `sumByCr_unapplied` is already grouped by CR.

### Concepts used:

1. Duplicate control
2. One value per CR
3. Aggregate lookup
4. Avoid duplicated total

---

# 46. Net Difference by CR

```sql
ROUND
(
    Difference
    - Deductions
    - ABS(Unapplied)
,0)
```

### Meaning:

Final per-CR difference after deduction and unapplied adjustment.

### Concepts used:

1. Final reconciliation formula
2. `ROUND`
3. Difference minus deduction
4. Minus absolute unapplied
5. Per-CR calculation
6. Business logic column

---

# 47. `ROUND`

```sql
ROUND(value, 0)
```

### Meaning:

Round number to 0 decimal places.

Example:

```sql
ROUND(12.75, 0) = 13
```

---

# 48. Total Credit Amount Row

This row calculates total for all CR rows.

### Concepts used:

1. Total row
2. Sum of applied amount
3. Sum of A/R credit amount
4. Sum of difference
5. Total deductions
6. Total unapplied
7. Total net difference
8. Uses same column layout

---

# 49. Total Deductions

```sql
SELECT SUM(TRY_CAST(pay.total AS decimal(18,2)))
FROM dbo.step_03_payments_combined pay
INNER JOIN dbo.step_01a_cr_sheet c4
ON pay.cr_issue = c4.ref
WHERE pay.line_type IN ('Returns', 'Adjustments')
```

### Meaning:

Total deductions for all valid CRs.

### Concepts used:

1. Total SUM
2. Join payment to CR sheet
3. Only valid CRs
4. Filter Returns and Adjustments

---

# 50. Total Unapplied

```sql
SELECT SUM(x.sumByCr_unapplied)
FROM
(
    SELECT cr_issue,
           MAX(sumByCr_unapplied) AS sumByCr_unapplied
    FROM dbo.step_04b_order_not_matched
    GROUP BY cr_issue
) x
```

### Meaning:

First get one unapplied value per CR, then sum all.

### Concepts used:

1. Derived table
2. Group first
3. Sum after grouping
4. Avoid duplicate totals
5. Nested aggregation

---

# 51. Derived Table

```sql
FROM
(
    SELECT ...
) x
```

### Meaning:

A subquery used like a table.

### Concepts used:

1. Temporary result
2. Alias required
3. Used for grouped totals
4. Clean calculation step

---

# 52. Alias

Examples:

```sql
dbo.step_01a_cr_sheet c
dbo.step_03_payments_combined pay
dbo.step_04b_order_not_matched m
```

### Meaning:

Short name for table.

### Concepts used:

1. Cleaner query
2. Avoid long table names
3. Required in joins
4. Helps when same table used many times

---

# 53. Table Alias vs Column Alias

## Table alias:

```sql
dbo.step_01a_cr_sheet c
```

## Column alias:

```sql
c.ref AS [Credit #]
```

---

# 54. Dynamic Report Layout

Your final table is not normal raw data.

It is a **report table**.

### Concepts used:

1. Header row inside data
2. Month row inside data
3. CR rows
4. Summary rows
5. Blank spacing columns
6. Excel-style output
7. Invoice numbers as columns
8. Dynamic pivot-style layout

---

# 55. Pivot Concept Without `PIVOT`

You are making pivot manually using:

```sql
SUM(CASE WHEN invoice_number = 'IN#...' THEN qxc ELSE 0 END)
```

### Concepts used:

1. Manual pivot
2. Dynamic pivot
3. Invoice columns
4. CR rows
5. Conditional aggregation

---

# 56. Why Not Simple SELECT

Because invoice numbers change.

Today:

```sql
IN#1, IN#2
```

Tomorrow:

```sql
IN#3, IN#4, IN#5
```

So you need dynamic SQL.

---

# 57. Main Tables Used

## 1. `step_01a_cr_sheet`

Used for:

1. Credit numbers
2. CR amount
3. `ref`
4. `pay_day`

## 2. `step_01a_in_sheet`

Used for:

1. Invoice numbers
2. Invoice date
3. `ref`
4. `Ndatee`

## 3. `step_02_po_details`

Used for:

1. Invoice number
2. CR issue
3. Applied amount
4. `qxc`

## 4. `step_03_payments_combined`

Used for:

1. CR issue
2. Payment total
3. Deduction lines
4. Returns
5. Adjustments

## 5. `step_04b_order_not_matched`

Used for:

1. Unpaid invoice value
2. Unapplied CR value
3. Not-matched values

---

# 58. Main Business Logic

## CR row logic

For each CR#:

```text
Show amount applied under each invoice
Show total applied
Show A/R credit amount
Show difference
Show deductions
Show unapplied
Show net difference
```

---

## Invoice column logic

For each invoice:

```text
Create one dynamic column
Fill value based on matching invoice_number
```

---

## Total row logic

```text
Sum all CR rows
```

---

## Difference logic

```text
Difference = A/R Credit Amount - Credit Amount Applied
```

---

## Net Difference logic

```text
Net Difference by CR
=
ROUND(Difference - Deductions - ABS(Unapplied), 0)
```

---

# 59. Mini SQL Concepts Used

1. `DROP TABLE`
2. `IF EXISTS`
3. `GO`
4. `DECLARE`
5. Variables
6. `NVARCHAR(MAX)`
7. `SELECT @var =`
8. `STRING_AGG`
9. `QUOTENAME`
10. `REPLACE`
11. `LTRIM`
12. `RTRIM`
13. `DISTINCT`
14. `INNER JOIN`
15. `LEFT JOIN`
16. Subquery
17. Derived table
18. CTE
19. `WITH`
20. `WHERE`
21. `GROUP BY`
22. `HAVING`
23. `SUM`
24. `MAX`
25. `CASE WHEN`
26. `ISNULL`
27. `TRY_CAST`
28. `TRY_CONVERT`
29. `FORMAT`
30. `ABS`
31. `ROUND`
32. `CAST`
33. `varchar(50)`
34. `decimal(18,2)`
35. `NULL AS`
36. Column alias
37. Table alias
38. `UNION ALL`
39. `SELECT INTO`
40. `PRINT`
41. `EXEC`
42. `sp_executesql`
43. Dynamic SQL
44. Manual pivot
45. Report formatting
46. Conditional aggregation
47. Total row
48. Header row
49. Month row
50. Reconciliation logic

---

# 60. Ways to Solve This Problem

## Way 1: Your Current Way

Dynamic SQL + `SUM(CASE WHEN...)`

### Best for:

1. Excel-style report
2. Dynamic invoice columns
3. Custom rows
4. Blank columns
5. Full control

---

## Way 2: SQL Server `PIVOT`

Use SQL Server pivot feature.

### Good:

1. Cleaner pivot logic
2. Built for row-to-column

### Problem:

1. Still needs dynamic SQL
2. Harder for custom rows
3. Harder for deductions/unapplied/net difference

---

## Way 3: Normal Table First, Excel Pivot Later

Create simple table like:

```text
Credit #
Invoice #
Applied Amount
AR Amount
Difference
Deductions
Unapplied
Net Difference
```

Then create Excel pivot from it.

### Good:

1. Cleaner SQL
2. Easier debugging
3. Better database design

### Problem:

1. Excel formatting needed separately
2. Not final report directly

---

## Way 4: Python Generate Excel

SQL gives clean data, Python creates formatted Excel.

### Good:

1. Best formatting control
2. Easier Excel layout
3. Easier styling
4. Easier formulas

### Problem:

1. Needs Python code
2. More steps

---

## Way 5: Stored Procedure

Put this full query inside:

```sql
CREATE OR ALTER PROCEDURE dbo.sp_step07
AS
BEGIN
   ...
END
```

### Good:

1. Reusable
2. Easy to run
3. Can add logs
4. Can call from master procedure

---

# 61. Best Learning Order for You

Learn this query in this order:

1. `SELECT`
2. `WHERE`
3. `JOIN`
4. `GROUP BY`
5. `SUM`
6. `CASE WHEN`
7. `SUM(CASE WHEN...)`
8. `STRING_AGG`
9. `QUOTENAME`
10. Dynamic SQL
11. `UNION ALL`
12. `SELECT INTO`
13. CTE
14. Subquery
15. Final report layout

---

# 62. Core Idea in One Simple Line

Your query does this:

```text
Take CR#, take IN#, match them through PO/payment tables, calculate applied/difference/deductions/unapplied/net difference, then create an Excel-style dynamic report table.
```





























/*==============================================================
 MS SQL SERVER - A TO Z SYNTAX PRACTICE
 Format : SQL
 Level  : Beginner
 Tables : Sample names only
 Style  : Small chunks
==============================================================*/


/*==============================================================
 01. BASIC SELECT
 Purpose: Show data from a table
==============================================================*/

SELECT *
FROM dbo.sample_table;


/*==============================================================
 02. SELECT SPECIFIC COLUMNS
 Purpose: Show only selected columns
==============================================================*/

SELECT
    id,
    customer_name,
    amount
FROM dbo.sample_table;


/*==============================================================
 03. COLUMN ALIAS
 Purpose: Rename column in output
==============================================================*/

SELECT
    customer_name AS [Customer Name],
    amount        AS [Total Amount]
FROM dbo.sample_table;


/*==============================================================
 04. WHERE
 Purpose: Filter rows
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE amount > 100;


/*==============================================================
 05. WHERE WITH TEXT
 Purpose: Filter text value
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE customer_name = 'Ali';


/*==============================================================
 06. WHERE WITH MULTIPLE CONDITIONS
 Purpose: AND means both conditions must be true
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE amount > 100
  AND status = 'Active';


/*==============================================================
 07. OR CONDITION
 Purpose: Any one condition can be true
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE status = 'Active'
   OR status = 'Pending';


/*==============================================================
 08. IN
 Purpose: Match multiple values
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE status IN ('Active', 'Pending', 'Hold');


/*==============================================================
 09. NOT IN
 Purpose: Exclude multiple values
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE status NOT IN ('Cancel', 'Delete');


/*==============================================================
 10. LIKE
 Purpose: Search text pattern
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE customer_name LIKE 'A%';


/*==============================================================
 11. LIKE CONTAINS
 Purpose: Search text anywhere
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE customer_name LIKE '%Ali%';


/*==============================================================
 12. IS NULL
 Purpose: Find blank/null values
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE invoice_number IS NULL;


/*==============================================================
 13. IS NOT NULL
 Purpose: Find rows where value exists
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE invoice_number IS NOT NULL;


/*==============================================================
 14. ORDER BY ASC
 Purpose: Sort small to big / A to Z
==============================================================*/

SELECT *
FROM dbo.sample_table
ORDER BY amount ASC;


/*==============================================================
 15. ORDER BY DESC
 Purpose: Sort big to small / Z to A
==============================================================*/

SELECT *
FROM dbo.sample_table
ORDER BY amount DESC;


/*==============================================================
 16. TOP
 Purpose: Show limited rows
==============================================================*/

SELECT TOP 10 *
FROM dbo.sample_table;


/*==============================================================
 17. DISTINCT
 Purpose: Remove duplicate values
==============================================================*/

SELECT DISTINCT
    status
FROM dbo.sample_table;


/*==============================================================
 18. COUNT
 Purpose: Count rows
==============================================================*/

SELECT
    COUNT(*) AS total_rows
FROM dbo.sample_table;


/*==============================================================
 19. SUM
 Purpose: Add values
==============================================================*/

SELECT
    SUM(amount) AS total_amount
FROM dbo.sample_table;


/*==============================================================
 20. AVG
 Purpose: Average value
==============================================================*/

SELECT
    AVG(amount) AS average_amount
FROM dbo.sample_table;


/*==============================================================
 21. MIN AND MAX
 Purpose: Lowest and highest value
==============================================================*/

SELECT
    MIN(amount) AS minimum_amount,
    MAX(amount) AS maximum_amount
FROM dbo.sample_table;


/*==============================================================
 22. GROUP BY
 Purpose: Make totals by group
==============================================================*/

SELECT
    status,
    SUM(amount) AS total_amount
FROM dbo.sample_table
GROUP BY status;


/*==============================================================
 23. GROUP BY WITH COUNT
 Purpose: Count rows by group
==============================================================*/

SELECT
    status,
    COUNT(*) AS total_count
FROM dbo.sample_table
GROUP BY status;


/*==============================================================
 24. HAVING
 Purpose: Filter after GROUP BY
==============================================================*/

SELECT
    status,
    SUM(amount) AS total_amount
FROM dbo.sample_table
GROUP BY status
HAVING SUM(amount) > 1000;


/*==============================================================
 25. CASE
 Purpose: Make IF ELSE logic
==============================================================*/

SELECT
    customer_name,
    amount,
    CASE
        WHEN amount > 1000 THEN 'Big Amount'
        WHEN amount > 500  THEN 'Medium Amount'
        ELSE 'Small Amount'
    END AS amount_type
FROM dbo.sample_table;


/*==============================================================
 26. ISNULL
 Purpose: Replace NULL with value
==============================================================*/

SELECT
    customer_name,
    ISNULL(invoice_number, 'No Invoice') AS invoice_number
FROM dbo.sample_table;


/*==============================================================
 27. COALESCE
 Purpose: Pick first non-null value
==============================================================*/

SELECT
    COALESCE(invoice_number, order_number, 'No Value') AS final_value
FROM dbo.sample_table;


/*==============================================================
 28. CAST
 Purpose: Convert data type
==============================================================*/

SELECT
    CAST(amount AS decimal(18,2)) AS amount_decimal
FROM dbo.sample_table;


/*==============================================================
 29. TRY_CAST
 Purpose: Safe convert. Bad value becomes NULL
==============================================================*/

SELECT
    TRY_CAST(amount AS decimal(18,2)) AS safe_amount
FROM dbo.sample_table;


/*==============================================================
 30. CONVERT DATE
 Purpose: Convert text to date
==============================================================*/

SELECT
    TRY_CONVERT(date, order_date) AS clean_date
FROM dbo.sample_table;


/*==============================================================
 31. FORMAT DATE
 Purpose: Show date in month-year format
==============================================================*/

SELECT
    FORMAT(TRY_CONVERT(date, order_date), 'MMM-yy') AS month_year
FROM dbo.sample_table;


/*==============================================================
 32. DATEPART
 Purpose: Get part of date
==============================================================*/

SELECT
    DATEPART(YEAR, TRY_CONVERT(date, order_date))  AS order_year,
    DATEPART(MONTH, TRY_CONVERT(date, order_date)) AS order_month
FROM dbo.sample_table;


/*==============================================================
 33. GETDATE
 Purpose: Current date and time
==============================================================*/

SELECT
    GETDATE() AS current_datetime;


/*==============================================================
 34. STRING CONCAT
 Purpose: Join text values
==============================================================*/

SELECT
    CONCAT(customer_name, ' - ', invoice_number) AS customer_invoice
FROM dbo.sample_table;


/*==============================================================
 35. LTRIM RTRIM
 Purpose: Remove extra spaces
==============================================================*/

SELECT
    LTRIM(RTRIM(customer_name)) AS clean_customer_name
FROM dbo.sample_table;


/*==============================================================
 36. LEN
 Purpose: Count text length
==============================================================*/

SELECT
    customer_name,
    LEN(customer_name) AS name_length
FROM dbo.sample_table;


/*==============================================================
 37. LEFT RIGHT SUBSTRING
 Purpose: Pick part of text
==============================================================*/

SELECT
    LEFT(customer_name, 3)        AS first_3_letters,
    RIGHT(customer_name, 3)       AS last_3_letters,
    SUBSTRING(customer_name, 2, 4) AS middle_letters
FROM dbo.sample_table;


/*==============================================================
 38. REPLACE
 Purpose: Replace text
==============================================================*/

SELECT
    REPLACE(customer_name, 'Ali', 'Ahmed') AS updated_name
FROM dbo.sample_table;


/*==============================================================
 39. INNER JOIN
 Purpose: Only matching rows from both tables
==============================================================*/

SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
INNER JOIN dbo.orders b
    ON a.id = b.customer_id;


/*==============================================================
 40. LEFT JOIN
 Purpose: All rows from left table + matching from right
==============================================================*/

SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
LEFT JOIN dbo.orders b
    ON a.id = b.customer_id;


/*==============================================================
 41. RIGHT JOIN
 Purpose: All rows from right table + matching from left
==============================================================*/

SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
RIGHT JOIN dbo.orders b
    ON a.id = b.customer_id;


/*==============================================================
 42. FULL OUTER JOIN
 Purpose: All rows from both tables
==============================================================*/

SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
FULL OUTER JOIN dbo.orders b
    ON a.id = b.customer_id;


/*==============================================================
 43. CROSS JOIN
 Purpose: Every row from table A joins every row from table B
==============================================================*/

SELECT
    a.customer_name,
    b.product_name
FROM dbo.customers a
CROSS JOIN dbo.products b;


/*==============================================================
 44. UNION ALL
 Purpose: Combine results and keep duplicates
==============================================================*/

SELECT customer_name
FROM dbo.customers_2025

UNION ALL

SELECT customer_name
FROM dbo.customers_2026;


/*==============================================================
 45. UNION
 Purpose: Combine results and remove duplicates
==============================================================*/

SELECT customer_name
FROM dbo.customers_2025

UNION

SELECT customer_name
FROM dbo.customers_2026;


/*==============================================================
 46. CTE
 Purpose: Temporary named result
==============================================================*/

;WITH sales_cte AS
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
)
SELECT *
FROM sales_cte;


/*==============================================================
 47. CTE WITH JOIN
 Purpose: Use CTE result with another table
==============================================================*/

;WITH sales_cte AS
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
)
SELECT
    c.customer_name,
    s.total_amount
FROM sales_cte s
INNER JOIN dbo.customers c
    ON s.customer_id = c.id;


/*==============================================================
 48. SUBQUERY
 Purpose: Query inside query
==============================================================*/

SELECT *
FROM dbo.orders
WHERE amount >
(
    SELECT AVG(amount)
    FROM dbo.orders
);


/*==============================================================
 49. EXISTS
 Purpose: Check if matching row exists
==============================================================*/

SELECT *
FROM dbo.customers c
WHERE EXISTS
(
    SELECT 1
    FROM dbo.orders o
    WHERE o.customer_id = c.id
);


/*==============================================================
 50. NOT EXISTS
 Purpose: Find rows with no match
==============================================================*/

SELECT *
FROM dbo.customers c
WHERE NOT EXISTS
(
    SELECT 1
    FROM dbo.orders o
    WHERE o.customer_id = c.id
);


/*==============================================================
 51. INSERT
 Purpose: Add new row
==============================================================*/

INSERT INTO dbo.customers
(
    customer_name,
    city,
    status
)
VALUES
(
    'Ali',
    'Lahore',
    'Active'
);


/*==============================================================
 52. INSERT MULTIPLE ROWS
 Purpose: Add many rows
==============================================================*/

INSERT INTO dbo.customers
(
    customer_name,
    city,
    status
)
VALUES
('Ali',   'Lahore',  'Active'),
('Ahmed', 'Karachi', 'Active'),
('Sara',  'Islamabad', 'Pending');


/*==============================================================
 53. UPDATE
 Purpose: Change existing data
==============================================================*/

UPDATE dbo.customers
SET status = 'Active'
WHERE customer_name = 'Ali';


/*==============================================================
 54. UPDATE MULTIPLE COLUMNS
 Purpose: Change more than one column
==============================================================*/

UPDATE dbo.customers
SET
    city = 'Lahore',
    status = 'Active'
WHERE id = 1;


/*==============================================================
 55. UPDATE WITH JOIN
 Purpose: Update using another table
==============================================================*/

UPDATE c
SET c.total_amount = o.total_amount
FROM dbo.customers c
INNER JOIN
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
) o
    ON c.id = o.customer_id;


/*==============================================================
 56. DELETE
 Purpose: Delete rows
==============================================================*/

DELETE FROM dbo.customers
WHERE status = 'Delete';


/*==============================================================
 57. CREATE TABLE
 Purpose: Make new table
==============================================================*/

CREATE TABLE dbo.sample_new_table
(
    id            INT IDENTITY(1,1) PRIMARY KEY,
    customer_name VARCHAR(100),
    amount        DECIMAL(18,2),
    order_date    DATE,
    status        VARCHAR(50)
);


/*==============================================================
 58. DROP TABLE
 Purpose: Delete table completely
==============================================================*/

DROP TABLE IF EXISTS dbo.sample_new_table;


/*==============================================================
 59. ALTER TABLE ADD COLUMN
 Purpose: Add new column
==============================================================*/

ALTER TABLE dbo.customers
ADD phone_number VARCHAR(50);


/*==============================================================
 60. ALTER TABLE ALTER COLUMN
 Purpose: Change column data type
==============================================================*/

ALTER TABLE dbo.customers
ALTER COLUMN phone_number VARCHAR(100);


/*==============================================================
 61. ALTER TABLE DROP COLUMN
 Purpose: Remove column
==============================================================*/

ALTER TABLE dbo.customers
DROP COLUMN phone_number;


/*==============================================================
 62. SELECT INTO
 Purpose: Create new table from query result
==============================================================*/

SELECT
    customer_id,
    SUM(amount) AS total_amount
INTO dbo.customer_sales_summary
FROM dbo.orders
GROUP BY customer_id;


/*==============================================================
 63. TEMP TABLE
 Purpose: Temporary table
==============================================================*/

CREATE TABLE #temp_sales
(
    customer_id INT,
    total_amount DECIMAL(18,2)
);

INSERT INTO #temp_sales
SELECT
    customer_id,
    SUM(amount)
FROM dbo.orders
GROUP BY customer_id;

SELECT *
FROM #temp_sales;


/*==============================================================
 64. VIEW
 Purpose: Saved SELECT query
==============================================================*/

CREATE VIEW dbo.vw_customer_sales
AS
SELECT
    c.customer_name,
    SUM(o.amount) AS total_amount
FROM dbo.customers c
INNER JOIN dbo.orders o
    ON c.id = o.customer_id
GROUP BY c.customer_name;


/*==============================================================
 65. USE VIEW
 Purpose: Read view like table
==============================================================*/

SELECT *
FROM dbo.vw_customer_sales;


/*==============================================================
 66. STORED PROCEDURE
 Purpose: Saved SQL work
==============================================================*/

CREATE PROCEDURE dbo.sp_show_customers
AS
BEGIN
    SELECT *
    FROM dbo.customers;
END;


/*==============================================================
 67. EXEC PROCEDURE
 Purpose: Run stored procedure
==============================================================*/

EXEC dbo.sp_show_customers;


/*==============================================================
 68. PROCEDURE WITH PARAMETER
 Purpose: Send value into procedure
==============================================================*/

CREATE PROCEDURE dbo.sp_customer_by_status
    @status VARCHAR(50)
AS
BEGIN
    SELECT *
    FROM dbo.customers
    WHERE status = @status;
END;


/*==============================================================
 69. EXEC PROCEDURE WITH PARAMETER
 Purpose: Run procedure with value
==============================================================*/

EXEC dbo.sp_customer_by_status @status = 'Active';


/*==============================================================
 70. VARIABLE
 Purpose: Store value
==============================================================*/

DECLARE @amount DECIMAL(18,2);

SET @amount = 1000;

SELECT @amount AS my_amount;


/*==============================================================
 71. IF ELSE
 Purpose: Conditional logic
==============================================================*/

DECLARE @total DECIMAL(18,2);

SET @total = 500;

IF @total > 1000
BEGIN
    SELECT 'Big Amount' AS result;
END
ELSE
BEGIN
    SELECT 'Small Amount' AS result;
END;


/*==============================================================
 72. WHILE LOOP
 Purpose: Repeat work
==============================================================*/

DECLARE @counter INT;

SET @counter = 1;

WHILE @counter <= 5
BEGIN
    SELECT @counter AS current_number;

    SET @counter = @counter + 1;
END;


/*==============================================================
 73. TRY CATCH
 Purpose: Handle errors
==============================================================*/

BEGIN TRY

    SELECT 10 / 0 AS result;

END TRY
BEGIN CATCH

    SELECT
        ERROR_MESSAGE() AS error_message,
        ERROR_LINE()    AS error_line;

END CATCH;


/*==============================================================
 74. TRANSACTION
 Purpose: Save or rollback changes
==============================================================*/

BEGIN TRANSACTION;

UPDATE dbo.customers
SET status = 'Active'
WHERE id = 1;

COMMIT TRANSACTION;


/*==============================================================
 75. TRANSACTION WITH ROLLBACK
 Purpose: Undo changes if needed
==============================================================*/

BEGIN TRANSACTION;

UPDATE dbo.customers
SET status = 'Wrong'
WHERE id = 1;

ROLLBACK TRANSACTION;


/*==============================================================
 76. ROW_NUMBER
 Purpose: Give row numbers
==============================================================*/

SELECT
    ROW_NUMBER() OVER (ORDER BY amount DESC) AS row_no,
    customer_name,
    amount
FROM dbo.sample_table;


/*==============================================================
 77. RANK
 Purpose: Ranking with gaps
==============================================================*/

SELECT
    RANK() OVER (ORDER BY amount DESC) AS rank_no,
    customer_name,
    amount
FROM dbo.sample_table;


/*==============================================================
 78. PARTITION BY
 Purpose: Row number inside each group
==============================================================*/

SELECT
    ROW_NUMBER() OVER
    (
        PARTITION BY status
        ORDER BY amount DESC
    ) AS row_no,
    status,
    customer_name,
    amount
FROM dbo.sample_table;


/*==============================================================
 79. PIVOT
 Purpose: Convert rows into columns
==============================================================*/

SELECT *
FROM
(
    SELECT
        customer_name,
        status,
        amount
    FROM dbo.sample_table
) src
PIVOT
(
    SUM(amount)
    FOR status IN ([Active], [Pending], [Hold])
) p;


/*==============================================================
 80. STRING_AGG
 Purpose: Combine many row values into one text
==============================================================*/

SELECT
    STRING_AGG(customer_name, ', ') AS all_customers
FROM dbo.customers;


/*==============================================================
 81. QUOTENAME
 Purpose: Safely make column name
==============================================================*/

SELECT
    QUOTENAME('IN#12345') AS safe_column_name;


/*==============================================================
 82. DYNAMIC SQL
 Purpose: Build SQL query as text and run it
==============================================================*/

DECLARE @sql NVARCHAR(MAX);

SET @sql = N'
SELECT *
FROM dbo.customers
WHERE status = ''Active'';
';

PRINT @sql;

EXEC sp_executesql @sql;


/*==============================================================
 83. DYNAMIC SQL WITH VARIABLE
 Purpose: Pass value safely
==============================================================*/

DECLARE @sql2 NVARCHAR(MAX);
DECLARE @status VARCHAR(50);

SET @status = 'Active';

SET @sql2 = N'
SELECT *
FROM dbo.customers
WHERE status = @p_status;
';

EXEC sp_executesql
    @sql2,
    N'@p_status VARCHAR(50)',
    @p_status = @status;


/*==============================================================
 84. CREATE INDEX
 Purpose: Make search faster
==============================================================*/

CREATE INDEX IX_orders_customer_id
ON dbo.orders(customer_id);


/*==============================================================
 85. DROP INDEX
 Purpose: Remove index
==============================================================*/

DROP INDEX IX_orders_customer_id
ON dbo.orders;


/*==============================================================
 86. PRIMARY KEY
 Purpose: Unique main ID
==============================================================*/

CREATE TABLE dbo.sample_pk_table
(
    id INT IDENTITY(1,1) PRIMARY KEY,
    name VARCHAR(100)
);


/*==============================================================
 87. FOREIGN KEY
 Purpose: Link child table with parent table
==============================================================*/

CREATE TABLE dbo.sample_child_table
(
    id INT IDENTITY(1,1) PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(18,2),

    CONSTRAINT FK_sample_child_customer
    FOREIGN KEY (customer_id)
    REFERENCES dbo.sample_pk_table(id)
);


/*==============================================================
 88. DEFAULT VALUE
 Purpose: Auto value if user does not give value
==============================================================*/

CREATE TABLE dbo.sample_default_table
(
    id INT IDENTITY(1,1),
    status VARCHAR(50) DEFAULT 'Active'
);


/*==============================================================
 89. CHECK CONSTRAINT
 Purpose: Allow only valid values
==============================================================*/

CREATE TABLE dbo.sample_check_table
(
    id INT IDENTITY(1,1),
    amount DECIMAL(18,2),

    CONSTRAINT CK_amount_positive
    CHECK (amount >= 0)
);


/*==============================================================
 90. UNIQUE CONSTRAINT
 Purpose: No duplicate value allowed
==============================================================*/

CREATE TABLE dbo.sample_unique_table
(
    id INT IDENTITY(1,1),
    email VARCHAR(100) UNIQUE
);


/*==============================================================
 91. MERGE
 Purpose: Insert or update in one statement
==============================================================*/

MERGE dbo.customers AS target
USING dbo.new_customers AS source
    ON target.id = source.id
WHEN MATCHED THEN
    UPDATE SET
        target.customer_name = source.customer_name,
        target.status = source.status
WHEN NOT MATCHED THEN
    INSERT
    (
        customer_name,
        status
    )
    VALUES
    (
        source.customer_name,
        source.status
    );


/*==============================================================
 92. OFFSET FETCH
 Purpose: Pagination
==============================================================*/

SELECT *
FROM dbo.customers
ORDER BY id
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;


/*==============================================================
 93. BETWEEN
 Purpose: Range filter
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE amount BETWEEN 100 AND 500;


/*==============================================================
 94. DATE RANGE
 Purpose: Filter dates
==============================================================*/

SELECT *
FROM dbo.sample_table
WHERE TRY_CONVERT(date, order_date) >= '2026-01-01'
  AND TRY_CONVERT(date, order_date) <  '2026-02-01';


/*==============================================================
 95. ABS
 Purpose: Make negative number positive
==============================================================*/

SELECT
    amount,
    ABS(amount) AS positive_amount
FROM dbo.sample_table;


/*==============================================================
 96. ROUND
 Purpose: Round number
==============================================================*/

SELECT
    amount,
    ROUND(amount, 2) AS rounded_amount
FROM dbo.sample_table;


/*==============================================================
 97. NULLIF
 Purpose: Avoid divide by zero
==============================================================*/

SELECT
    amount,
    quantity,
    amount / NULLIF(quantity, 0) AS unit_amount
FROM dbo.sample_table;


/*==============================================================
 98. IIF
 Purpose: Short IF condition
==============================================================*/

SELECT
    customer_name,
    amount,
    IIF(amount > 1000, 'Big', 'Small') AS amount_type
FROM dbo.sample_table;


/*==============================================================
 99. DROP OBJECTS SAFELY
 Purpose: Clean sample objects
==============================================================*/

DROP TABLE IF EXISTS dbo.sample_pk_table;
DROP TABLE IF EXISTS dbo.sample_child_table;
DROP TABLE IF EXISTS dbo.sample_default_table;
DROP TABLE IF EXISTS dbo.sample_check_table;
DROP TABLE IF EXISTS dbo.sample_unique_table;


/*==============================================================
 100. FINAL FULL STRUCTURE EXAMPLE
 Purpose: Common query head to body structure
==============================================================*/

SELECT
    c.customer_name,
    c.city,
    o.order_number,
    TRY_CAST(o.amount AS decimal(18,2)) AS amount,

    CASE
        WHEN TRY_CAST(o.amount AS decimal(18,2)) > 1000 THEN 'Big'
        WHEN TRY_CAST(o.amount AS decimal(18,2)) > 500  THEN 'Medium'
        ELSE 'Small'
    END AS amount_type,

    FORMAT(TRY_CONVERT(date, o.order_date), 'MMM-yy') AS month_year

FROM dbo.customers c

INNER JOIN dbo.orders o
    ON c.id = o.customer_id

WHERE c.status = 'Active'
  AND o.order_date IS NOT NULL

GROUP BY
    c.customer_name,
    c.city,
    o.order_number,
    o.amount,
    o.order_date

HAVING SUM(TRY_CAST(o.amount AS decimal(18,2))) > 100

ORDER BY
    c.customer_name ASC,
    amount DESC;






























# MS SQL Server - A to Z Syntax Practice

**Format:** SQL  
**Level:** Beginner  
**Tables:** Sample names only  
**Style:** Small chunks  

---

## 01. BASIC SELECT

**Purpose:** Show data from a table.

```sql
SELECT *
FROM dbo.sample_table;
```

---

## 02. SELECT SPECIFIC COLUMNS

**Purpose:** Show only selected columns.

```sql
SELECT
    id,
    customer_name,
    amount
FROM dbo.sample_table;
```

---

## 03. COLUMN ALIAS

**Purpose:** Rename column in output.

```sql
SELECT
    customer_name AS [Customer Name],
    amount        AS [Total Amount]
FROM dbo.sample_table;
```

---

## 04. WHERE

**Purpose:** Filter rows.

```sql
SELECT *
FROM dbo.sample_table
WHERE amount > 100;
```

---

## 05. WHERE WITH TEXT

**Purpose:** Filter text value.

```sql
SELECT *
FROM dbo.sample_table
WHERE customer_name = 'Ali';
```

---

## 06. WHERE WITH MULTIPLE CONDITIONS

**Purpose:** AND means both conditions must be true.

```sql
SELECT *
FROM dbo.sample_table
WHERE amount > 100
  AND status = 'Active';
```

---

## 07. OR CONDITION

**Purpose:** Any one condition can be true.

```sql
SELECT *
FROM dbo.sample_table
WHERE status = 'Active'
   OR status = 'Pending';
```

---

## 08. IN

**Purpose:** Match multiple values.

```sql
SELECT *
FROM dbo.sample_table
WHERE status IN ('Active', 'Pending', 'Hold');
```

---

## 09. NOT IN

**Purpose:** Exclude multiple values.

```sql
SELECT *
FROM dbo.sample_table
WHERE status NOT IN ('Cancel', 'Delete');
```

---

## 10. LIKE

**Purpose:** Search text pattern.

```sql
SELECT *
FROM dbo.sample_table
WHERE customer_name LIKE 'A%';
```

---

## 11. LIKE CONTAINS

**Purpose:** Search text anywhere.

```sql
SELECT *
FROM dbo.sample_table
WHERE customer_name LIKE '%Ali%';
```

---

## 12. IS NULL

**Purpose:** Find blank/null values.

```sql
SELECT *
FROM dbo.sample_table
WHERE invoice_number IS NULL;
```

---

## 13. IS NOT NULL

**Purpose:** Find rows where value exists.

```sql
SELECT *
FROM dbo.sample_table
WHERE invoice_number IS NOT NULL;
```

---

## 14. ORDER BY ASC

**Purpose:** Sort small to big / A to Z.

```sql
SELECT *
FROM dbo.sample_table
ORDER BY amount ASC;
```

---

## 15. ORDER BY DESC

**Purpose:** Sort big to small / Z to A.

```sql
SELECT *
FROM dbo.sample_table
ORDER BY amount DESC;
```

---

## 16. TOP

**Purpose:** Show limited rows.

```sql
SELECT TOP 10 *
FROM dbo.sample_table;
```

---

## 17. DISTINCT

**Purpose:** Remove duplicate values.

```sql
SELECT DISTINCT
    status
FROM dbo.sample_table;
```

---

## 18. COUNT

**Purpose:** Count rows.

```sql
SELECT
    COUNT(*) AS total_rows
FROM dbo.sample_table;
```

---

## 19. SUM

**Purpose:** Add values.

```sql
SELECT
    SUM(amount) AS total_amount
FROM dbo.sample_table;
```

---

## 20. AVG

**Purpose:** Average value.

```sql
SELECT
    AVG(amount) AS average_amount
FROM dbo.sample_table;
```

---

## 21. MIN AND MAX

**Purpose:** Lowest and highest value.

```sql
SELECT
    MIN(amount) AS minimum_amount,
    MAX(amount) AS maximum_amount
FROM dbo.sample_table;
```

---

## 22. GROUP BY

**Purpose:** Make totals by group.

```sql
SELECT
    status,
    SUM(amount) AS total_amount
FROM dbo.sample_table
GROUP BY status;
```

---

## 23. GROUP BY WITH COUNT

**Purpose:** Count rows by group.

```sql
SELECT
    status,
    COUNT(*) AS total_count
FROM dbo.sample_table
GROUP BY status;
```

---

## 24. HAVING

**Purpose:** Filter after GROUP BY.

```sql
SELECT
    status,
    SUM(amount) AS total_amount
FROM dbo.sample_table
GROUP BY status
HAVING SUM(amount) > 1000;
```

---

## 25. CASE

**Purpose:** Make IF ELSE logic.

```sql
SELECT
    customer_name,
    amount,
    CASE
        WHEN amount > 1000 THEN 'Big Amount'
        WHEN amount > 500  THEN 'Medium Amount'
        ELSE 'Small Amount'
    END AS amount_type
FROM dbo.sample_table;
```

---

## 26. ISNULL

**Purpose:** Replace NULL with value.

```sql
SELECT
    customer_name,
    ISNULL(invoice_number, 'No Invoice') AS invoice_number
FROM dbo.sample_table;
```

---

## 27. COALESCE

**Purpose:** Pick first non-null value.

```sql
SELECT
    COALESCE(invoice_number, order_number, 'No Value') AS final_value
FROM dbo.sample_table;
```

---

## 28. CAST

**Purpose:** Convert data type.

```sql
SELECT
    CAST(amount AS decimal(18,2)) AS amount_decimal
FROM dbo.sample_table;
```

---

## 29. TRY_CAST

**Purpose:** Safe convert. Bad value becomes NULL.

```sql
SELECT
    TRY_CAST(amount AS decimal(18,2)) AS safe_amount
FROM dbo.sample_table;
```

---

## 30. CONVERT DATE

**Purpose:** Convert text to date.

```sql
SELECT
    TRY_CONVERT(date, order_date) AS clean_date
FROM dbo.sample_table;
```

---

## 31. FORMAT DATE

**Purpose:** Show date in month-year format.

```sql
SELECT
    FORMAT(TRY_CONVERT(date, order_date), 'MMM-yy') AS month_year
FROM dbo.sample_table;
```

---

## 32. DATEPART

**Purpose:** Get part of date.

```sql
SELECT
    DATEPART(YEAR, TRY_CONVERT(date, order_date))  AS order_year,
    DATEPART(MONTH, TRY_CONVERT(date, order_date)) AS order_month
FROM dbo.sample_table;
```

---

## 33. GETDATE

**Purpose:** Current date and time.

```sql
SELECT
    GETDATE() AS current_datetime;
```

---

## 34. STRING CONCAT

**Purpose:** Join text values.

```sql
SELECT
    CONCAT(customer_name, ' - ', invoice_number) AS customer_invoice
FROM dbo.sample_table;
```

---

## 35. LTRIM RTRIM

**Purpose:** Remove extra spaces.

```sql
SELECT
    LTRIM(RTRIM(customer_name)) AS clean_customer_name
FROM dbo.sample_table;
```

---

## 36. LEN

**Purpose:** Count text length.

```sql
SELECT
    customer_name,
    LEN(customer_name) AS name_length
FROM dbo.sample_table;
```

---

## 37. LEFT RIGHT SUBSTRING

**Purpose:** Pick part of text.

```sql
SELECT
    LEFT(customer_name, 3)         AS first_3_letters,
    RIGHT(customer_name, 3)        AS last_3_letters,
    SUBSTRING(customer_name, 2, 4) AS middle_letters
FROM dbo.sample_table;
```

---

## 38. REPLACE

**Purpose:** Replace text.

```sql
SELECT
    REPLACE(customer_name, 'Ali', 'Ahmed') AS updated_name
FROM dbo.sample_table;
```

---

## 39. INNER JOIN

**Purpose:** Only matching rows from both tables.

```sql
SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
INNER JOIN dbo.orders b
    ON a.id = b.customer_id;
```

---

## 40. LEFT JOIN

**Purpose:** All rows from left table + matching from right.

```sql
SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
LEFT JOIN dbo.orders b
    ON a.id = b.customer_id;
```

---

## 41. RIGHT JOIN

**Purpose:** All rows from right table + matching from left.

```sql
SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
RIGHT JOIN dbo.orders b
    ON a.id = b.customer_id;
```

---

## 42. FULL OUTER JOIN

**Purpose:** All rows from both tables.

```sql
SELECT
    a.id,
    a.customer_name,
    b.order_number,
    b.amount
FROM dbo.customers a
FULL OUTER JOIN dbo.orders b
    ON a.id = b.customer_id;
```

---

## 43. CROSS JOIN

**Purpose:** Every row from table A joins every row from table B.

```sql
SELECT
    a.customer_name,
    b.product_name
FROM dbo.customers a
CROSS JOIN dbo.products b;
```

---

## 44. UNION ALL

**Purpose:** Combine results and keep duplicates.

```sql
SELECT customer_name
FROM dbo.customers_2025

UNION ALL

SELECT customer_name
FROM dbo.customers_2026;
```

---

## 45. UNION

**Purpose:** Combine results and remove duplicates.

```sql
SELECT customer_name
FROM dbo.customers_2025

UNION

SELECT customer_name
FROM dbo.customers_2026;
```

---

## 46. CTE

**Purpose:** Temporary named result.

```sql
;WITH sales_cte AS
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
)
SELECT *
FROM sales_cte;
```

---

## 47. CTE WITH JOIN

**Purpose:** Use CTE result with another table.

```sql
;WITH sales_cte AS
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
)
SELECT
    c.customer_name,
    s.total_amount
FROM sales_cte s
INNER JOIN dbo.customers c
    ON s.customer_id = c.id;
```

---

## 48. SUBQUERY

**Purpose:** Query inside query.

```sql
SELECT *
FROM dbo.orders
WHERE amount >
(
    SELECT AVG(amount)
    FROM dbo.orders
);
```

---

## 49. EXISTS

**Purpose:** Check if matching row exists.

```sql
SELECT *
FROM dbo.customers c
WHERE EXISTS
(
    SELECT 1
    FROM dbo.orders o
    WHERE o.customer_id = c.id
);
```

---

## 50. NOT EXISTS

**Purpose:** Find rows with no match.

```sql
SELECT *
FROM dbo.customers c
WHERE NOT EXISTS
(
    SELECT 1
    FROM dbo.orders o
    WHERE o.customer_id = c.id
);
```

---

## 51. INSERT

**Purpose:** Add new row.

```sql
INSERT INTO dbo.customers
(
    customer_name,
    city,
    status
)
VALUES
(
    'Ali',
    'Lahore',
    'Active'
);
```

---

## 52. INSERT MULTIPLE ROWS

**Purpose:** Add many rows.

```sql
INSERT INTO dbo.customers
(
    customer_name,
    city,
    status
)
VALUES
('Ali',   'Lahore',    'Active'),
('Ahmed', 'Karachi',   'Active'),
('Sara',  'Islamabad', 'Pending');
```

---

## 53. UPDATE

**Purpose:** Change existing data.

```sql
UPDATE dbo.customers
SET status = 'Active'
WHERE customer_name = 'Ali';
```

---

## 54. UPDATE MULTIPLE COLUMNS

**Purpose:** Change more than one column.

```sql
UPDATE dbo.customers
SET
    city = 'Lahore',
    status = 'Active'
WHERE id = 1;
```

---

## 55. UPDATE WITH JOIN

**Purpose:** Update using another table.

```sql
UPDATE c
SET c.total_amount = o.total_amount
FROM dbo.customers c
INNER JOIN
(
    SELECT
        customer_id,
        SUM(amount) AS total_amount
    FROM dbo.orders
    GROUP BY customer_id
) o
    ON c.id = o.customer_id;
```

---

## 56. DELETE

**Purpose:** Delete rows.

```sql
DELETE FROM dbo.customers
WHERE status = 'Delete';
```

---

## 57. CREATE TABLE

**Purpose:** Make new table.

```sql
CREATE TABLE dbo.sample_new_table
(
    id            INT IDENTITY(1,1) PRIMARY KEY,
    customer_name VARCHAR(100),
    amount        DECIMAL(18,2),
    order_date    DATE,
    status        VARCHAR(50)
);
```

---

## 58. DROP TABLE

**Purpose:** Delete table completely.

```sql
DROP TABLE IF EXISTS dbo.sample_new_table;
```

---

## 59. ALTER TABLE ADD COLUMN

**Purpose:** Add new column.

```sql
ALTER TABLE dbo.customers
ADD phone_number VARCHAR(50);
```

---

## 60. ALTER TABLE ALTER COLUMN

**Purpose:** Change column data type.

```sql
ALTER TABLE dbo.customers
ALTER COLUMN phone_number VARCHAR(100);
```

---

## 61. ALTER TABLE DROP COLUMN

**Purpose:** Remove column.

```sql
ALTER TABLE dbo.customers
DROP COLUMN phone_number;
```

---

## 62. SELECT INTO

**Purpose:** Create new table from query result.

```sql
SELECT
    customer_id,
    SUM(amount) AS total_amount
INTO dbo.customer_sales_summary
FROM dbo.orders
GROUP BY customer_id;
```

---

## 63. TEMP TABLE

**Purpose:** Temporary table.

```sql
CREATE TABLE #temp_sales
(
    customer_id INT,
    total_amount DECIMAL(18,2)
);

INSERT INTO #temp_sales
SELECT
    customer_id,
    SUM(amount)
FROM dbo.orders
GROUP BY customer_id;

SELECT *
FROM #temp_sales;
```

---

## 64. VIEW

**Purpose:** Saved SELECT query.

```sql
CREATE VIEW dbo.vw_customer_sales
AS
SELECT
    c.customer_name,
    SUM(o.amount) AS total_amount
FROM dbo.customers c
INNER JOIN dbo.orders o
    ON c.id = o.customer_id
GROUP BY c.customer_name;
```

---

## 65. USE VIEW

**Purpose:** Read view like table.

```sql
SELECT *
FROM dbo.vw_customer_sales;
```

---

## 66. STORED PROCEDURE

**Purpose:** Saved SQL work.

```sql
CREATE PROCEDURE dbo.sp_show_customers
AS
BEGIN
    SELECT *
    FROM dbo.customers;
END;
```

---

## 67. EXEC PROCEDURE

**Purpose:** Run stored procedure.

```sql
EXEC dbo.sp_show_customers;
```

---

## 68. PROCEDURE WITH PARAMETER

**Purpose:** Send value into procedure.

```sql
CREATE PROCEDURE dbo.sp_customer_by_status
    @status VARCHAR(50)
AS
BEGIN
    SELECT *
    FROM dbo.customers
    WHERE status = @status;
END;
```

---

## 69. EXEC PROCEDURE WITH PARAMETER

**Purpose:** Run procedure with value.

```sql
EXEC dbo.sp_customer_by_status @status = 'Active';
```

---

## 70. VARIABLE

**Purpose:** Store value.

```sql
DECLARE @amount DECIMAL(18,2);

SET @amount = 1000;

SELECT @amount AS my_amount;
```

---

## 71. IF ELSE

**Purpose:** Conditional logic.

```sql
DECLARE @total DECIMAL(18,2);

SET @total = 500;

IF @total > 1000
BEGIN
    SELECT 'Big Amount' AS result;
END
ELSE
BEGIN
    SELECT 'Small Amount' AS result;
END;
```

---

## 72. WHILE LOOP

**Purpose:** Repeat work.

```sql
DECLARE @counter INT;

SET @counter = 1;

WHILE @counter <= 5
BEGIN
    SELECT @counter AS current_number;

    SET @counter = @counter + 1;
END;
```

---

## 73. TRY CATCH

**Purpose:** Handle errors.

```sql
BEGIN TRY

    SELECT 10 / 0 AS result;

END TRY
BEGIN CATCH

    SELECT
        ERROR_MESSAGE() AS error_message,
        ERROR_LINE()    AS error_line;

END CATCH;
```

---

## 74. TRANSACTION

**Purpose:** Save changes.

```sql
BEGIN TRANSACTION;

UPDATE dbo.customers
SET status = 'Active'
WHERE id = 1;

COMMIT TRANSACTION;
```

---

## 75. TRANSACTION WITH ROLLBACK

**Purpose:** Undo changes if needed.

```sql
BEGIN TRANSACTION;

UPDATE dbo.customers
SET status = 'Wrong'
WHERE id = 1;

ROLLBACK TRANSACTION;
```

---

## 76. ROW_NUMBER

**Purpose:** Give row numbers.

```sql
SELECT
    ROW_NUMBER() OVER (ORDER BY amount DESC) AS row_no,
    customer_name,
    amount
FROM dbo.sample_table;
```

---

## 77. RANK

**Purpose:** Ranking with gaps.

```sql
SELECT
    RANK() OVER (ORDER BY amount DESC) AS rank_no,
    customer_name,
    amount
FROM dbo.sample_table;
```

---

## 78. PARTITION BY

**Purpose:** Row number inside each group.

```sql
SELECT
    ROW_NUMBER() OVER
    (
        PARTITION BY status
        ORDER BY amount DESC
    ) AS row_no,
    status,
    customer_name,
    amount
FROM dbo.sample_table;
```

---

## 79. PIVOT

**Purpose:** Convert rows into columns.

```sql
SELECT *
FROM
(
    SELECT
        customer_name,
        status,
        amount
    FROM dbo.sample_table
) src
PIVOT
(
    SUM(amount)
    FOR status IN ([Active], [Pending], [Hold])
) p;
```

---

## 80. STRING_AGG

**Purpose:** Combine many row values into one text.

```sql
SELECT
    STRING_AGG(customer_name, ', ') AS all_customers
FROM dbo.customers;
```

---

## 81. QUOTENAME

**Purpose:** Safely make column name.

```sql
SELECT
    QUOTENAME('IN#12345') AS safe_column_name;
```

---

## 82. DYNAMIC SQL

**Purpose:** Build SQL query as text and run it.

```sql
DECLARE @sql NVARCHAR(MAX);

SET @sql = N'
SELECT *
FROM dbo.customers
WHERE status = ''Active'';
';

PRINT @sql;

EXEC sp_executesql @sql;
```

---

## 83. DYNAMIC SQL WITH VARIABLE

**Purpose:** Pass value safely.

```sql
DECLARE @sql2 NVARCHAR(MAX);
DECLARE @status VARCHAR(50);

SET @status = 'Active';

SET @sql2 = N'
SELECT *
FROM dbo.customers
WHERE status = @p_status;
';

EXEC sp_executesql
    @sql2,
    N'@p_status VARCHAR(50)',
    @p_status = @status;
```

---

## 84. CREATE INDEX

**Purpose:** Make search faster.

```sql
CREATE INDEX IX_orders_customer_id
ON dbo.orders(customer_id);
```

---

## 85. DROP INDEX

**Purpose:** Remove index.

```sql
DROP INDEX IX_orders_customer_id
ON dbo.orders;
```

---

## 86. PRIMARY KEY

**Purpose:** Unique main ID.

```sql
CREATE TABLE dbo.sample_pk_table
(
    id INT IDENTITY(1,1) PRIMARY KEY,
    name VARCHAR(100)
);
```

---

## 87. FOREIGN KEY

**Purpose:** Link child table with parent table.

```sql
CREATE TABLE dbo.sample_child_table
(
    id INT IDENTITY(1,1) PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(18,2),

    CONSTRAINT FK_sample_child_customer
    FOREIGN KEY (customer_id)
    REFERENCES dbo.sample_pk_table(id)
);
```

---

## 88. DEFAULT VALUE

**Purpose:** Auto value if user does not give value.

```sql
CREATE TABLE dbo.sample_default_table
(
    id INT IDENTITY(1,1),
    status VARCHAR(50) DEFAULT 'Active'
);
```

---

## 89. CHECK CONSTRAINT

**Purpose:** Allow only valid values.

```sql
CREATE TABLE dbo.sample_check_table
(
    id INT IDENTITY(1,1),
    amount DECIMAL(18,2),

    CONSTRAINT CK_amount_positive
    CHECK (amount >= 0)
);
```

---

## 90. UNIQUE CONSTRAINT

**Purpose:** No duplicate value allowed.

```sql
CREATE TABLE dbo.sample_unique_table
(
    id INT IDENTITY(1,1),
    email VARCHAR(100) UNIQUE
);
```

---

## 91. MERGE

**Purpose:** Insert or update in one statement.

```sql
MERGE dbo.customers AS target
USING dbo.new_customers AS source
    ON target.id = source.id
WHEN MATCHED THEN
    UPDATE SET
        target.customer_name = source.customer_name,
        target.status = source.status
WHEN NOT MATCHED THEN
    INSERT
    (
        customer_name,
        status
    )
    VALUES
    (
        source.customer_name,
        source.status
    );
```

---

## 92. OFFSET FETCH

**Purpose:** Pagination.

```sql
SELECT *
FROM dbo.customers
ORDER BY id
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;
```

---

## 93. BETWEEN

**Purpose:** Range filter.

```sql
SELECT *
FROM dbo.sample_table
WHERE amount BETWEEN 100 AND 500;
```

---

## 94. DATE RANGE

**Purpose:** Filter dates.

```sql
SELECT *
FROM dbo.sample_table
WHERE TRY_CONVERT(date, order_date) >= '2026-01-01'
  AND TRY_CONVERT(date, order_date) <  '2026-02-01';
```

---

## 95. ABS

**Purpose:** Make negative number positive.

```sql
SELECT
    amount,
    ABS(amount) AS positive_amount
FROM dbo.sample_table;
```

---

## 96. ROUND

**Purpose:** Round number.

```sql
SELECT
    amount,
    ROUND(amount, 2) AS rounded_amount
FROM dbo.sample_table;
```

---

## 97. NULLIF

**Purpose:** Avoid divide by zero.

```sql
SELECT
    amount,
    quantity,
    amount / NULLIF(quantity, 0) AS unit_amount
FROM dbo.sample_table;
```

---

## 98. IIF

**Purpose:** Short IF condition.

```sql
SELECT
    customer_name,
    amount,
    IIF(amount > 1000, 'Big', 'Small') AS amount_type
FROM dbo.sample_table;
```

---

## 99. DROP OBJECTS SAFELY

**Purpose:** Clean sample objects.

```sql
DROP TABLE IF EXISTS dbo.sample_pk_table;
DROP TABLE IF EXISTS dbo.sample_child_table;
DROP TABLE IF EXISTS dbo.sample_default_table;
DROP TABLE IF EXISTS dbo.sample_check_table;
DROP TABLE IF EXISTS dbo.sample_unique_table;
```

---

## 100. FINAL FULL STRUCTURE EXAMPLE

**Purpose:** Common query head to body structure.

```sql
SELECT
    c.customer_name,
    c.city,
    o.order_number,
    TRY_CAST(o.amount AS decimal(18,2)) AS amount,

    CASE
        WHEN TRY_CAST(o.amount AS decimal(18,2)) > 1000 THEN 'Big'
        WHEN TRY_CAST(o.amount AS decimal(18,2)) > 500  THEN 'Medium'
        ELSE 'Small'
    END AS amount_type,

    FORMAT(TRY_CONVERT(date, o.order_date), 'MMM-yy') AS month_year

FROM dbo.customers c

INNER JOIN dbo.orders o
    ON c.id = o.customer_id

WHERE c.status = 'Active'
  AND o.order_date IS NOT NULL

GROUP BY
    c.customer_name,
    c.city,
    o.order_number,
    o.amount,
    o.order_date

HAVING SUM(TRY_CAST(o.amount AS decimal(18,2))) > 100

ORDER BY
    c.customer_name ASC,
    amount DESC;
```
