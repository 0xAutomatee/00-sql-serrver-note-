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
