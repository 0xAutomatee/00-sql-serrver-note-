# MS SQL Server Syntax Only - Beginner Notes

This file uses simple sample tables only.

Sample table names used:

```sql
dbo.Students
dbo.Courses
dbo.Orders
dbo.Customers
dbo.Products
```

---

## 01. SELECT All Columns

**Syntax:**

```sql
SELECT *
FROM table_name;
```

**Example:**

```sql
SELECT *
FROM dbo.Students;
```

---

## 02. SELECT Specific Columns

**Syntax:**

```sql
SELECT column1, column2
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentID,
    StudentName
FROM dbo.Students;
```

---

## 03. Column Alias

**Syntax:**

```sql
SELECT column_name AS [New Name]
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName AS [Student Name],
    City        AS [Student City]
FROM dbo.Students;
```

---

## 04. WHERE

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE condition;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE City = 'Lahore';
```

---

## 05. AND

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE condition1
  AND condition2;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE City = 'Lahore'
  AND Age > 18;
```

---

## 06. OR

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE condition1
   OR condition2;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE City = 'Lahore'
   OR City = 'Karachi';
```

---

## 07. IN

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name IN (value1, value2, value3);
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE City IN ('Lahore', 'Karachi', 'Islamabad');
```

---

## 08. NOT IN

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name NOT IN (value1, value2);
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE City NOT IN ('Lahore', 'Karachi');
```

---

## 09. LIKE Starts With

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name LIKE 'A%';
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE StudentName LIKE 'A%';
```

---

## 10. LIKE Contains

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name LIKE '%text%';
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE StudentName LIKE '%Ali%';
```

---

## 11. IS NULL

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name IS NULL;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE PhoneNumber IS NULL;
```

---

## 12. IS NOT NULL

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name IS NOT NULL;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
WHERE PhoneNumber IS NOT NULL;
```

---

## 13. ORDER BY ASC

**Syntax:**

```sql
SELECT *
FROM table_name
ORDER BY column_name ASC;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
ORDER BY StudentName ASC;
```

---

## 14. ORDER BY DESC

**Syntax:**

```sql
SELECT *
FROM table_name
ORDER BY column_name DESC;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
ORDER BY Age DESC;
```

---

## 15. TOP

**Syntax:**

```sql
SELECT TOP number *
FROM table_name;
```

**Example:**

```sql
SELECT TOP 5 *
FROM dbo.Students;
```

---

## 16. DISTINCT

**Syntax:**

```sql
SELECT DISTINCT column_name
FROM table_name;
```

**Example:**

```sql
SELECT DISTINCT City
FROM dbo.Students;
```

---

## 17. COUNT

**Syntax:**

```sql
SELECT COUNT(*) AS total_count
FROM table_name;
```

**Example:**

```sql
SELECT COUNT(*) AS total_students
FROM dbo.Students;
```

---

## 18. SUM

**Syntax:**

```sql
SELECT SUM(column_name) AS total_value
FROM table_name;
```

**Example:**

```sql
SELECT SUM(Amount) AS total_amount
FROM dbo.Orders;
```

---

## 19. AVG

**Syntax:**

```sql
SELECT AVG(column_name) AS average_value
FROM table_name;
```

**Example:**

```sql
SELECT AVG(Age) AS average_age
FROM dbo.Students;
```

---

## 20. MIN and MAX

**Syntax:**

```sql
SELECT
    MIN(column_name) AS minimum_value,
    MAX(column_name) AS maximum_value
FROM table_name;
```

**Example:**

```sql
SELECT
    MIN(Age) AS youngest_age,
    MAX(Age) AS oldest_age
FROM dbo.Students;
```

---

## 21. GROUP BY

**Syntax:**

```sql
SELECT
    column_name,
    COUNT(*) AS total_count
FROM table_name
GROUP BY column_name;
```

**Example:**

```sql
SELECT
    City,
    COUNT(*) AS total_students
FROM dbo.Students
GROUP BY City;
```

---

## 22. GROUP BY With SUM

**Syntax:**

```sql
SELECT
    column_name,
    SUM(amount_column) AS total_amount
FROM table_name
GROUP BY column_name;
```

**Example:**

```sql
SELECT
    CustomerID,
    SUM(Amount) AS total_amount
FROM dbo.Orders
GROUP BY CustomerID;
```

---

## 23. HAVING

**Syntax:**

```sql
SELECT
    column_name,
    SUM(amount_column) AS total_amount
FROM table_name
GROUP BY column_name
HAVING SUM(amount_column) > value;
```

**Example:**

```sql
SELECT
    CustomerID,
    SUM(Amount) AS total_amount
FROM dbo.Orders
GROUP BY CustomerID
HAVING SUM(Amount) > 1000;
```

---

## 24. CASE

**Syntax:**

```sql
SELECT
    column_name,
    CASE
        WHEN condition THEN result1
        ELSE result2
    END AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName,
    Age,
    CASE
        WHEN Age >= 18 THEN 'Adult'
        ELSE 'Minor'
    END AS AgeGroup
FROM dbo.Students;
```

---

## 25. ISNULL

**Syntax:**

```sql
SELECT ISNULL(column_name, replacement_value) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName,
    ISNULL(PhoneNumber, 'No Phone') AS PhoneNumber
FROM dbo.Students;
```

---

## 26. COALESCE

**Syntax:**

```sql
SELECT COALESCE(column1, column2, default_value) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName,
    COALESCE(PhoneNumber, Email, 'No Contact') AS ContactInfo
FROM dbo.Students;
```

---

## 27. CAST

**Syntax:**

```sql
SELECT CAST(column_name AS data_type) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    CAST(Amount AS decimal(18,2)) AS AmountDecimal
FROM dbo.Orders;
```

---

## 28. TRY_CAST

**Syntax:**

```sql
SELECT TRY_CAST(column_name AS data_type) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    TRY_CAST(Amount AS decimal(18,2)) AS SafeAmount
FROM dbo.Orders;
```

---

## 29. TRY_CONVERT

**Syntax:**

```sql
SELECT TRY_CONVERT(data_type, column_name) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    TRY_CONVERT(date, OrderDate) AS CleanOrderDate
FROM dbo.Orders;
```

---

## 30. FORMAT Date

**Syntax:**

```sql
SELECT FORMAT(date_column, 'format') AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    FORMAT(TRY_CONVERT(date, OrderDate), 'MMM-yyyy') AS OrderMonth
FROM dbo.Orders;
```

---

## 31. GETDATE

**Syntax:**

```sql
SELECT GETDATE() AS current_date_time;
```

**Example:**

```sql
SELECT GETDATE() AS TodayDateTime;
```

---

## 32. DATEPART

**Syntax:**

```sql
SELECT DATEPART(part, date_column) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    DATEPART(YEAR, TRY_CONVERT(date, OrderDate)) AS OrderYear
FROM dbo.Orders;
```

---

## 33. CONCAT

**Syntax:**

```sql
SELECT CONCAT(column1, column2) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    CONCAT(FirstName, ' ', LastName) AS FullName
FROM dbo.Students;
```

---

## 34. LTRIM and RTRIM

**Syntax:**

```sql
SELECT LTRIM(RTRIM(column_name)) AS clean_column
FROM table_name;
```

**Example:**

```sql
SELECT
    LTRIM(RTRIM(StudentName)) AS CleanStudentName
FROM dbo.Students;
```

---

## 35. LEN

**Syntax:**

```sql
SELECT LEN(column_name) AS length_column
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName,
    LEN(StudentName) AS NameLength
FROM dbo.Students;
```

---

## 36. LEFT

**Syntax:**

```sql
SELECT LEFT(column_name, number_of_characters) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    LEFT(StudentName, 3) AS FirstThreeLetters
FROM dbo.Students;
```

---

## 37. RIGHT

**Syntax:**

```sql
SELECT RIGHT(column_name, number_of_characters) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    RIGHT(StudentName, 3) AS LastThreeLetters
FROM dbo.Students;
```

---

## 38. SUBSTRING

**Syntax:**

```sql
SELECT SUBSTRING(column_name, start_position, length) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    SUBSTRING(StudentName, 2, 4) AS NamePart
FROM dbo.Students;
```

---

## 39. REPLACE

**Syntax:**

```sql
SELECT REPLACE(column_name, old_text, new_text) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    REPLACE(StudentName, 'Ali', 'Ahmed') AS UpdatedName
FROM dbo.Students;
```

---

## 40. INNER JOIN

**Syntax:**

```sql
SELECT
    a.column_name,
    b.column_name
FROM table1 a
INNER JOIN table2 b
    ON a.common_column = b.common_column;
```

**Example:**

```sql
SELECT
    c.CustomerName,
    o.OrderID,
    o.Amount
FROM dbo.Customers c
INNER JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID;
```

---

## 41. LEFT JOIN

**Syntax:**

```sql
SELECT
    a.column_name,
    b.column_name
FROM table1 a
LEFT JOIN table2 b
    ON a.common_column = b.common_column;
```

**Example:**

```sql
SELECT
    c.CustomerName,
    o.OrderID,
    o.Amount
FROM dbo.Customers c
LEFT JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID;
```

---

## 42. RIGHT JOIN

**Syntax:**

```sql
SELECT
    a.column_name,
    b.column_name
FROM table1 a
RIGHT JOIN table2 b
    ON a.common_column = b.common_column;
```

**Example:**

```sql
SELECT
    c.CustomerName,
    o.OrderID,
    o.Amount
FROM dbo.Customers c
RIGHT JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID;
```

---

## 43. FULL OUTER JOIN

**Syntax:**

```sql
SELECT
    a.column_name,
    b.column_name
FROM table1 a
FULL OUTER JOIN table2 b
    ON a.common_column = b.common_column;
```

**Example:**

```sql
SELECT
    c.CustomerName,
    o.OrderID
FROM dbo.Customers c
FULL OUTER JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID;
```

---

## 44. CROSS JOIN

**Syntax:**

```sql
SELECT
    a.column_name,
    b.column_name
FROM table1 a
CROSS JOIN table2 b;
```

**Example:**

```sql
SELECT
    s.StudentName,
    c.CourseName
FROM dbo.Students s
CROSS JOIN dbo.Courses c;
```

---

## 45. UNION ALL

**Syntax:**

```sql
SELECT column_name FROM table1
UNION ALL
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT StudentName FROM dbo.Students2025
UNION ALL
SELECT StudentName FROM dbo.Students2026;
```

---

## 46. UNION

**Syntax:**

```sql
SELECT column_name FROM table1
UNION
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT City FROM dbo.Students
UNION
SELECT City FROM dbo.Customers;
```

---

## 47. CTE

**Syntax:**

```sql
;WITH cte_name AS
(
    SELECT columns
    FROM table_name
)
SELECT *
FROM cte_name;
```

**Example:**

```sql
;WITH StudentList AS
(
    SELECT
        StudentID,
        StudentName,
        City
    FROM dbo.Students
)
SELECT *
FROM StudentList;
```

---

## 48. Subquery

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name > 
(
    SELECT value
    FROM table_name
);
```

**Example:**

```sql
SELECT *
FROM dbo.Orders
WHERE Amount >
(
    SELECT AVG(Amount)
    FROM dbo.Orders
);
```

---

## 49. EXISTS

**Syntax:**

```sql
SELECT *
FROM table1 a
WHERE EXISTS
(
    SELECT 1
    FROM table2 b
    WHERE b.common_column = a.common_column
);
```

**Example:**

```sql
SELECT *
FROM dbo.Customers c
WHERE EXISTS
(
    SELECT 1
    FROM dbo.Orders o
    WHERE o.CustomerID = c.CustomerID
);
```

---

## 50. NOT EXISTS

**Syntax:**

```sql
SELECT *
FROM table1 a
WHERE NOT EXISTS
(
    SELECT 1
    FROM table2 b
    WHERE b.common_column = a.common_column
);
```

**Example:**

```sql
SELECT *
FROM dbo.Customers c
WHERE NOT EXISTS
(
    SELECT 1
    FROM dbo.Orders o
    WHERE o.CustomerID = c.CustomerID
);
```

---

## 51. INSERT

**Syntax:**

```sql
INSERT INTO table_name
(
    column1,
    column2
)
VALUES
(
    value1,
    value2
);
```

**Example:**

```sql
INSERT INTO dbo.Students
(
    StudentName,
    City
)
VALUES
(
    'Ali',
    'Lahore'
);
```

---

## 52. INSERT Multiple Rows

**Syntax:**

```sql
INSERT INTO table_name
(
    column1,
    column2
)
VALUES
(value1, value2),
(value3, value4);
```

**Example:**

```sql
INSERT INTO dbo.Students
(
    StudentName,
    City
)
VALUES
('Ali', 'Lahore'),
('Sara', 'Karachi');
```

---

## 53. UPDATE

**Syntax:**

```sql
UPDATE table_name
SET column_name = new_value
WHERE condition;
```

**Example:**

```sql
UPDATE dbo.Students
SET City = 'Islamabad'
WHERE StudentID = 1;
```

---

## 54. UPDATE Multiple Columns

**Syntax:**

```sql
UPDATE table_name
SET
    column1 = value1,
    column2 = value2
WHERE condition;
```

**Example:**

```sql
UPDATE dbo.Students
SET
    City = 'Lahore',
    Age = 20
WHERE StudentID = 1;
```

---

## 55. UPDATE With JOIN

**Syntax:**

```sql
UPDATE a
SET a.column_name = b.column_name
FROM table1 a
INNER JOIN table2 b
    ON a.common_column = b.common_column;
```

**Example:**

```sql
UPDATE c
SET c.LastOrderAmount = o.Amount
FROM dbo.Customers c
INNER JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID;
```

---

## 56. DELETE

**Syntax:**

```sql
DELETE FROM table_name
WHERE condition;
```

**Example:**

```sql
DELETE FROM dbo.Students
WHERE StudentID = 10;
```

---

## 57. CREATE TABLE

**Syntax:**

```sql
CREATE TABLE table_name
(
    column1 data_type,
    column2 data_type
);
```

**Example:**

```sql
CREATE TABLE dbo.Students
(
    StudentID   INT IDENTITY(1,1) PRIMARY KEY,
    StudentName VARCHAR(100),
    Age         INT,
    City        VARCHAR(100)
);
```

---

## 58. DROP TABLE

**Syntax:**

```sql
DROP TABLE IF EXISTS table_name;
```

**Example:**

```sql
DROP TABLE IF EXISTS dbo.Students;
```

---

## 59. ALTER TABLE ADD Column

**Syntax:**

```sql
ALTER TABLE table_name
ADD new_column data_type;
```

**Example:**

```sql
ALTER TABLE dbo.Students
ADD Email VARCHAR(100);
```

---

## 60. ALTER TABLE ALTER Column

**Syntax:**

```sql
ALTER TABLE table_name
ALTER COLUMN column_name new_data_type;
```

**Example:**

```sql
ALTER TABLE dbo.Students
ALTER COLUMN Email VARCHAR(200);
```

---

## 61. ALTER TABLE DROP Column

**Syntax:**

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

**Example:**

```sql
ALTER TABLE dbo.Students
DROP COLUMN Email;
```

---

## 62. SELECT INTO

**Syntax:**

```sql
SELECT columns
INTO new_table
FROM old_table;
```

**Example:**

```sql
SELECT
    StudentID,
    StudentName,
    City
INTO dbo.Students_Backup
FROM dbo.Students;
```

---

## 63. Temporary Table

**Syntax:**

```sql
CREATE TABLE #temp_table
(
    column1 data_type,
    column2 data_type
);
```

**Example:**

```sql
CREATE TABLE #TempStudents
(
    StudentID INT,
    StudentName VARCHAR(100)
);

INSERT INTO #TempStudents
SELECT
    StudentID,
    StudentName
FROM dbo.Students;

SELECT *
FROM #TempStudents;
```

---

## 64. VIEW

**Syntax:**

```sql
CREATE VIEW view_name
AS
SELECT columns
FROM table_name;
```

**Example:**

```sql
CREATE VIEW dbo.vw_Students
AS
SELECT
    StudentID,
    StudentName,
    City
FROM dbo.Students;
```

---

## 65. Use VIEW

**Syntax:**

```sql
SELECT *
FROM view_name;
```

**Example:**

```sql
SELECT *
FROM dbo.vw_Students;
```

---

## 66. STORED PROCEDURE

**Syntax:**

```sql
CREATE PROCEDURE procedure_name
AS
BEGIN
    SQL statement;
END;
```

**Example:**

```sql
CREATE PROCEDURE dbo.sp_ShowStudents
AS
BEGIN
    SELECT *
    FROM dbo.Students;
END;
```

---

## 67. EXEC Procedure

**Syntax:**

```sql
EXEC procedure_name;
```

**Example:**

```sql
EXEC dbo.sp_ShowStudents;
```

---

## 68. Procedure With Parameter

**Syntax:**

```sql
CREATE PROCEDURE procedure_name
    @parameter_name data_type
AS
BEGIN
    SELECT *
    FROM table_name
    WHERE column_name = @parameter_name;
END;
```

**Example:**

```sql
CREATE PROCEDURE dbo.sp_StudentsByCity
    @City VARCHAR(100)
AS
BEGIN
    SELECT *
    FROM dbo.Students
    WHERE City = @City;
END;
```

---

## 69. EXEC Procedure With Parameter

**Syntax:**

```sql
EXEC procedure_name @parameter_name = value;
```

**Example:**

```sql
EXEC dbo.sp_StudentsByCity @City = 'Lahore';
```

---

## 70. Variable

**Syntax:**

```sql
DECLARE @variable_name data_type;

SET @variable_name = value;

SELECT @variable_name;
```

**Example:**

```sql
DECLARE @StudentAge INT;

SET @StudentAge = 20;

SELECT @StudentAge AS StudentAge;
```

---

## 71. IF ELSE

**Syntax:**

```sql
IF condition
BEGIN
    statement;
END
ELSE
BEGIN
    statement;
END;
```

**Example:**

```sql
DECLARE @Age INT;

SET @Age = 20;

IF @Age >= 18
BEGIN
    SELECT 'Adult' AS Result;
END
ELSE
BEGIN
    SELECT 'Minor' AS Result;
END;
```

---

## 72. WHILE Loop

**Syntax:**

```sql
WHILE condition
BEGIN
    statement;
END;
```

**Example:**

```sql
DECLARE @Counter INT;

SET @Counter = 1;

WHILE @Counter <= 5
BEGIN
    SELECT @Counter AS NumberValue;

    SET @Counter = @Counter + 1;
END;
```

---

## 73. TRY CATCH

**Syntax:**

```sql
BEGIN TRY
    statement;
END TRY
BEGIN CATCH
    statement;
END CATCH;
```

**Example:**

```sql
BEGIN TRY
    SELECT 10 / 0 AS Result;
END TRY
BEGIN CATCH
    SELECT ERROR_MESSAGE() AS ErrorMessage;
END CATCH;
```

---

## 74. TRANSACTION COMMIT

**Syntax:**

```sql
BEGIN TRANSACTION;

statement;

COMMIT TRANSACTION;
```

**Example:**

```sql
BEGIN TRANSACTION;

UPDATE dbo.Students
SET City = 'Lahore'
WHERE StudentID = 1;

COMMIT TRANSACTION;
```

---

## 75. TRANSACTION ROLLBACK

**Syntax:**

```sql
BEGIN TRANSACTION;

statement;

ROLLBACK TRANSACTION;
```

**Example:**

```sql
BEGIN TRANSACTION;

UPDATE dbo.Students
SET City = 'Wrong City'
WHERE StudentID = 1;

ROLLBACK TRANSACTION;
```

---

## 76. ROW_NUMBER

**Syntax:**

```sql
SELECT
    ROW_NUMBER() OVER (ORDER BY column_name) AS row_number,
    columns
FROM table_name;
```

**Example:**

```sql
SELECT
    ROW_NUMBER() OVER (ORDER BY StudentName) AS RowNo,
    StudentName,
    City
FROM dbo.Students;
```

---

## 77. RANK

**Syntax:**

```sql
SELECT
    RANK() OVER (ORDER BY column_name) AS rank_number,
    columns
FROM table_name;
```

**Example:**

```sql
SELECT
    RANK() OVER (ORDER BY Marks DESC) AS RankNo,
    StudentName,
    Marks
FROM dbo.Students;
```

---

## 78. PARTITION BY

**Syntax:**

```sql
SELECT
    ROW_NUMBER() OVER
    (
        PARTITION BY group_column
        ORDER BY sort_column
    ) AS row_number,
    columns
FROM table_name;
```

**Example:**

```sql
SELECT
    ROW_NUMBER() OVER
    (
        PARTITION BY City
        ORDER BY StudentName
    ) AS RowNo,
    StudentName,
    City
FROM dbo.Students;
```

---

## 79. PIVOT

**Syntax:**

```sql
SELECT *
FROM
(
    SELECT row_column, pivot_column, value_column
    FROM table_name
) source_table
PIVOT
(
    SUM(value_column)
    FOR pivot_column IN ([Column1], [Column2])
) pivot_table;
```

**Example:**

```sql
SELECT *
FROM
(
    SELECT
        StudentName,
        SubjectName,
        Marks
    FROM dbo.StudentMarks
) src
PIVOT
(
    SUM(Marks)
    FOR SubjectName IN ([Math], [English], [Computer])
) p;
```

---

## 80. STRING_AGG

**Syntax:**

```sql
SELECT STRING_AGG(column_name, separator) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    STRING_AGG(StudentName, ', ') AS AllStudents
FROM dbo.Students;
```

---

## 81. QUOTENAME

**Syntax:**

```sql
SELECT QUOTENAME(value) AS new_column_name;
```

**Example:**

```sql
SELECT QUOTENAME('Student Name') AS SafeColumnName;
```

---

## 82. Dynamic SQL

**Syntax:**

```sql
DECLARE @sql NVARCHAR(MAX);

SET @sql = N'SQL statement';

EXEC sp_executesql @sql;
```

**Example:**

```sql
DECLARE @sql NVARCHAR(MAX);

SET @sql = N'
SELECT *
FROM dbo.Students;
';

EXEC sp_executesql @sql;
```

---

## 83. Dynamic SQL With Parameter

**Syntax:**

```sql
DECLARE @sql NVARCHAR(MAX);

SET @sql = N'
SELECT *
FROM table_name
WHERE column_name = @parameter;
';

EXEC sp_executesql
    @sql,
    N'@parameter data_type',
    @parameter = value;
```

**Example:**

```sql
DECLARE @sql NVARCHAR(MAX);
DECLARE @City VARCHAR(100);

SET @City = 'Lahore';

SET @sql = N'
SELECT *
FROM dbo.Students
WHERE City = @CityName;
';

EXEC sp_executesql
    @sql,
    N'@CityName VARCHAR(100)',
    @CityName = @City;
```

---

## 84. CREATE INDEX

**Syntax:**

```sql
CREATE INDEX index_name
ON table_name(column_name);
```

**Example:**

```sql
CREATE INDEX IX_Students_City
ON dbo.Students(City);
```

---

## 85. DROP INDEX

**Syntax:**

```sql
DROP INDEX index_name
ON table_name;
```

**Example:**

```sql
DROP INDEX IX_Students_City
ON dbo.Students;
```

---

## 86. PRIMARY KEY

**Syntax:**

```sql
CREATE TABLE table_name
(
    id INT PRIMARY KEY,
    column_name data_type
);
```

**Example:**

```sql
CREATE TABLE dbo.Courses
(
    CourseID INT PRIMARY KEY,
    CourseName VARCHAR(100)
);
```

---

## 87. FOREIGN KEY

**Syntax:**

```sql
CREATE TABLE child_table
(
    id INT PRIMARY KEY,
    parent_id INT,

    CONSTRAINT constraint_name
    FOREIGN KEY (parent_id)
    REFERENCES parent_table(parent_id)
);
```

**Example:**

```sql
CREATE TABLE dbo.Enrollments
(
    EnrollmentID INT PRIMARY KEY,
    StudentID INT,

    CONSTRAINT FK_Enrollments_Students
    FOREIGN KEY (StudentID)
    REFERENCES dbo.Students(StudentID)
);
```

---

## 88. DEFAULT

**Syntax:**

```sql
CREATE TABLE table_name
(
    column_name data_type DEFAULT default_value
);
```

**Example:**

```sql
CREATE TABLE dbo.Products
(
    ProductID INT IDENTITY(1,1),
    ProductName VARCHAR(100),
    Status VARCHAR(50) DEFAULT 'Active'
);
```

---

## 89. CHECK

**Syntax:**

```sql
CREATE TABLE table_name
(
    column_name data_type,
    CONSTRAINT constraint_name CHECK (condition)
);
```

**Example:**

```sql
CREATE TABLE dbo.Products
(
    ProductID INT,
    Price DECIMAL(18,2),

    CONSTRAINT CK_Products_Price
    CHECK (Price >= 0)
);
```

---

## 90. UNIQUE

**Syntax:**

```sql
CREATE TABLE table_name
(
    column_name data_type UNIQUE
);
```

**Example:**

```sql
CREATE TABLE dbo.Users
(
    UserID INT,
    Email VARCHAR(100) UNIQUE
);
```

---

## 91. MERGE

**Syntax:**

```sql
MERGE target_table AS target
USING source_table AS source
    ON target.id = source.id
WHEN MATCHED THEN
    UPDATE SET target.column_name = source.column_name
WHEN NOT MATCHED THEN
    INSERT (column_name)
    VALUES (source.column_name);
```

**Example:**

```sql
MERGE dbo.Customers AS target
USING dbo.NewCustomers AS source
    ON target.CustomerID = source.CustomerID
WHEN MATCHED THEN
    UPDATE SET target.CustomerName = source.CustomerName
WHEN NOT MATCHED THEN
    INSERT (CustomerID, CustomerName)
    VALUES (source.CustomerID, source.CustomerName);
```

---

## 92. OFFSET FETCH

**Syntax:**

```sql
SELECT *
FROM table_name
ORDER BY column_name
OFFSET number ROWS
FETCH NEXT number ROWS ONLY;
```

**Example:**

```sql
SELECT *
FROM dbo.Students
ORDER BY StudentID
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;
```

---

## 93. BETWEEN

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE column_name BETWEEN start_value AND end_value;
```

**Example:**

```sql
SELECT *
FROM dbo.Orders
WHERE Amount BETWEEN 100 AND 500;
```

---

## 94. Date Range

**Syntax:**

```sql
SELECT *
FROM table_name
WHERE date_column >= start_date
  AND date_column < end_date;
```

**Example:**

```sql
SELECT *
FROM dbo.Orders
WHERE OrderDate >= '2026-01-01'
  AND OrderDate <  '2026-02-01';
```

---

## 95. ABS

**Syntax:**

```sql
SELECT ABS(number_column) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    ABS(-500) AS PositiveValue;
```

---

## 96. ROUND

**Syntax:**

```sql
SELECT ROUND(number_column, decimal_places) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    ROUND(123.4567, 2) AS RoundedValue;
```

---

## 97. NULLIF

**Syntax:**

```sql
SELECT value1 / NULLIF(value2, 0) AS result;
```

**Example:**

```sql
SELECT
    Amount / NULLIF(Quantity, 0) AS PriceEach
FROM dbo.Orders;
```

---

## 98. IIF

**Syntax:**

```sql
SELECT IIF(condition, true_result, false_result) AS new_column_name
FROM table_name;
```

**Example:**

```sql
SELECT
    StudentName,
    IIF(Age >= 18, 'Adult', 'Minor') AS AgeGroup
FROM dbo.Students;
```

---

## 99. Comments

**Single line comment:**

```sql
-- This is a single line comment
SELECT *
FROM dbo.Students;
```

**Multi line comment:**

```sql
/*
This is a multi line comment.
You can write many lines here.
*/
SELECT *
FROM dbo.Students;
```

---

## 100. Simple Full Query Structure

**Syntax order:**

```sql
SELECT
    columns
FROM table_name
JOIN other_table
    ON condition
WHERE condition
GROUP BY columns
HAVING group_condition
ORDER BY column_name;
```

**Example:**

```sql
SELECT
    c.CustomerName,
    SUM(o.Amount) AS TotalAmount
FROM dbo.Customers c
INNER JOIN dbo.Orders o
    ON c.CustomerID = o.CustomerID
WHERE c.City = 'Lahore'
GROUP BY c.CustomerName
HAVING SUM(o.Amount) > 1000
ORDER BY TotalAmount DESC;
```
