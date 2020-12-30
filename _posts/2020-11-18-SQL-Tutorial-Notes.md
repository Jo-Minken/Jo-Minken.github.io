---
layout: post
title: "SQL Tutorial Notes"
date: 2020-11-18
categories: [SQL]
---

A note taking from [tutorialspoint - SQL Tutorial](https://www.tutorialspoint.com/sql/index.htm)

## Overview
A note taking from [SQL Tutorial - Overview](https://www.tutorialspoint.com/sql/sql-overview.htm)

### SQL Process
When you are executing an SQL command for any RDBMS, the system determines the best way to carry out your request and SQL engine figures out how to interpret the task.
- Query Dispatcher
- Optimization Engines
- Class Query Engine
- SQL Query Engine

SQL Architecture (image from https://www.tutorialspoint.com/sql/sql-overview.htm)
![SQL Architecture](https://www.tutorialspoint.com/sql/images/sql-architecture.jpg)

### SQL commands
DDL - Data Definiton Language
- Create: Create new tables
- Alter: Modify existing tables
- Drop: Delete entire records and tables

DML - Data Manipulation Language
- Select
- Insert
- Update
- Delete

DCL - Data Control Language
- Grant: Give privileges to user
- Revoke: Take back granted privileges

## RDBMS Concepts
A note taking from [SQL Tutorial - RDBMS Concepts](https://www.tutorialspoint.com/sql/sql-rdbms-concepts.htm)

`NULL` value: A field with a NULL value is the one that has been left blank during a record creation.
NULL value is different than a zero value or a field that contains spaces

### SQL Constraints
A note taking from [SQL Tutorial - SQL Constraints](https://www.tutorialspoint.com/sql/sql-constraints.htm)
- [NOT NULL](https://www.tutorialspoint.com/sql/sql-not-null.htm)
- [DEFAULT](https://www.tutorialspoint.com/sql/sql-default.htm)
- [UNIQUE](https://www.tutorialspoint.com/sql/sql-unique.htm)
- [PRIMARY KEY](https://www.tutorialspoint.com/sql/sql-primary-key.htm)
- [FOREIGN KEY](https://www.tutorialspoint.com/sql/sql-foreign-key.htm)
- [CHECK](https://www.tutorialspoint.com/sql/sql-check.htm)
- [INDEX](https://www.tutorialspoint.com/sql/sql-index.htm)

### Database Normalization
- [First Normal Form (1NF)](https://www.tutorialspoint.com/sql/first-normal-form.htm)
    - Define the data items required as the columns in a table
    - Place the related data items in a table
    - Ensure that there are no repeating groups of data
    - Ensure that there is a primary key
- [Second Normal Form (2NF)](https://www.tutorialspoint.com/sql/second-normal-form.htm)
    The Second Normal Form states that it should meet all the rules for 1NF and there must be no partial dependences of any of the columns on the primary key
- [Third Normal Form (3NF)](https://www.tutorialspoint.com/sql/third-normal-form.htm)
    It is in second normal form and all nonprimary fields are dependent on the primary key<br>
    FYR: [Transitive dependency in DBMS](https://www.tutorialspoint.com/Transitive-dependency-in-DBMS)

## Syntax
A note taking from [SQL Tutorial - Syntax](https://www.tutorialspoint.com/sql/sql-syntax.htm)

```sql
# SELECT Statement
SELECT column1, column2....columnN
FROM   table_name;

# DISTINCT Clause
SELECT DISTINCT column1, column2....columnN
FROM   table_name
WHERE CONDITION;

# WHERE Clause
SELECT column1, column2....columnN
FROM table_name
WHERE CONDITION;

# AND/OR Clause
SELECT column1, column2....columnN
FROM table_name
WHERE CONDITION-1 {AND|OR} CONDITION-2;

# IN Clause
SELECT column1, column2....columnN
FROM table_name
WHERE column_name IN (val-1, val-2....val-N)

# BETWEEN Clause
SELECT column1, column2....columnN
FROM table_name
WHERE column_name BETWEEN val-1 AND val-2:

# LIKE Clause
SELECT column1, column2....columnN
FROM table_name
WHERE column_name LIKE { PATTERN };

// WHERE SALARY LIKE "200%"
// Finds any values that start with 200

// WHERE SALARY LIKE "%200"
// Finds any values that end with 200

// WHERE SALARY LIKE '%200%'
// Finds any values that have 200 in any position

// WHERE SALARY LIKE '_00%'
// Finds any values that have 00 in the second and third positions

// WHERE SALARY LIKE '2_%_%'
// Finds any values that start with 2 and are at least 3 characters in length

# ORDER BY Clause
SELECT column1, column2....columnN
FROM table_name
WHERE CONDITION
ORDER BY column_name { ASC|DESC };

# TOP, LIMIT, ROWNUM Clause
SELECT TOP 3 * FROM CUSTOMERS;

// Equivalent to:
SELECT * FROM CUSTOMERS LIMIT 3;

// Equivalent to: (Oracle SQL)
SELECT * FROM CUSTOMERS
WHERE ROWNUM <= 3;

# COUNT Clause
SELECT COUNT(column_name)
FROM table_name
WHERE CONDITION;

# GROUP BY Clause
SELECT SUM(column_name)
FROM   table_name
WHERE  CONDITION
GROUP BY column_name;

# HAVING Clause
SELECT SUM(column_name)
FROM table_name
WHERE  CONDITION
GROUP BY column_name
HAVING (arithematic function condition);

# INSERT INTO Statement
INSERT INTO TABLE_NAME (column1, column2, column3....columnN)  
VALUES (value1, value2, value3....valueN);

# CREATE TABLE Statement
CREATE TABLE table_name(
    column1 datatype,
    column2 datatype,
    column3 datatype,
    .....
    columnN datatype,
    PRIMARY KEY(column1, column2....columnN)
);

CREATE TABLE CUSTOMERS(
   ID   INT              NOT NULL,
   NAME VARCHAR (20)     NOT NULL,
   AGE  INT              NOT NULL,
   ADDRESS  CHAR (25) ,
   SALARY   DECIMAL (18, 2),       
   PRIMARY KEY (ID)
);

# DESCRIBE / DESC TABLE Statement
DESC table_name
// FYR: MySQLでテーブル構造を確認する(https://26gram.com/mysql-describe)

DESC CUSTOMERS;
// +---------+---------------+------+-----+---------+-------+
// | Field   | Type          | Null | Key | Default | Extra |
// +---------+---------------+------+-----+---------+-------+
// | ID      | int(11)       | NO   | PRI |         |       |
// | NAME    | varchar(20)   | NO   |     |         |       |
// | AGE     | int(11)       | NO   |     |         |       |
// | ADDRESS | char(25)      | YES  |     | NULL    |       |
// | SALARY  | decimal(18,2) | YES  |     | NULL    |       |
// +---------+---------------+------+-----+---------+-------+
// 5 rows in set (0.00 sec)

# DROP TABLE Statement
DROP TABLE table_name;

# CREATE INDEX Statement
CREATE UNIQUE INDEX index_name
ON table_name(column1, column2....columnN)

# TRUNCATE TABLE Statement
TRUNCATE TABLE table_name
// FYR: 全てのデータを削除する(TRUNCATE TABLE文)(https://www.dbonline.jp/mysql/insert/index12.html)

# ALTER TABLE Statement
ALTER TABLE table_name { ADD|DROP|MODIFY } column_name {data_type};
ALTER TABLE table_name RENAME TO new_table_name;
// FYR: SQL - ALTER TABLE Command(https://www.tutorialspoint.com/sql/sql-alter-command.htm)
// FYR: テーブル構造を変更する(ALTER TABLE文)(https://www.dbonline.jp/mysql/table/index18.html)

# INSERT INTO Statement
INSERT INTO table_name( column1, column2....columnN)
VALUES ( value1, value2....valueN);

# UPDATE Statement
UPDATE table_name
SET column1 = value1, column2 = value2....columnN=valueN
[ WHERE  CONDITION ];

# DELETE Statement
DELETE FROM table_name
[ WHERE  CONDITION ];

# CREATE DATABASE Statement
CREATE DATABASE database_name;

SHOW DATABASES;
// +--------------------+
// | Database           |
// +--------------------+
// | information_schema |
// | AMROOD             |
// | TUTORIALSPOINT     |
// | mysql              |
// | orig               |
// | test               |
// | testDB             |
// +--------------------+
// 7 rows in set (0.00 sec)

# DROP DATABASE Statement
DROP DATABASE database_name;

# USE Statement
USE database_name;
// FYR: 使用するデータベースを選択する(USE文)(https://www.dbonline.jp/mysql/database/index3.html)

# COMMIT Statement
COMMIT;
// FYR: データベースさわったこと無い新人向けトランザクション入門(https://qiita.com/komattio/items/838ea5df68eb076e8099)

# ROLLBACK Statement
ROLLBACK;
```

### Useful Functions
A note taking from [SQL Tutorial - Useful Functions](https://www.tutorialspoint.com/sql/sql-useful-functions.htm)

- [Count()](https://www.tutorialspoint.com/sql/sql-count-function.htm)
- [Max()](https://www.tutorialspoint.com/sql/sql-max-function.htm)
- [MIN()](https://www.tutorialspoint.com/sql/sql-min-function.htm)
- [AVG()](https://www.tutorialspoint.com/sql/sql-avg-function.htm)
- [SUM()](https://www.tutorialspoint.com/sql/sql-sum-function.htm)
- [SQRT()](https://www.tutorialspoint.com/sql/sql-sqrt-function.htm): used to find out the square root of a number
- [RAND()](https://www.tutorialspoint.com/sql/sql-rand-function.htm)
- [CONCAT()](https://www.tutorialspoint.com/sql/sql-concat-function.htm): used to concatenate two strings to a single string
- [Numeric()](https://www.tutorialspoint.com/sql/sql-numeric-functions.htm)
- [String()](https://www.tutorialspoint.com/sql/sql-string-functions.htm)

## Data Types
A note taking from [SQL Tutorial - Data Types](https://www.tutorialspoint.com/sql/sql-data-types.htm)

- Exact Numeric: bigint, int, smallint, tinyint, bit, decimal, numeric, money, smallmoney
- Approximate Numeric: float, real
- Date Time: datetime, smalldatetime, date, time
- Character Strings: char, varchar, varchar(max), text
- Unicode Character Strings: nchar, nvarchar, nvarchar(max), ntext
- Binary: binary, varbinary, varbinary(max), image
- Misc: sql_varient, timestamp, uniqueidentifier, xml, cursor, table

## Operators and Expressions
Skipped: [SQL Tutorial - Operators](https://www.tutorialspoint.com/sql/sql-operators.htm)

Comparison Operator `<>` checks if the values of two operands are equal or not, if values are not equal then condition becomes true.(standard SQL syntax which is equivalent to `!=`)

```sql
# select clause where field is not blank
select <field-name> from <table-name> where <field-name> <>'';
```

Date Expressions: CURRENT_TIMESTAMP
```sql
SELECT CURRENT_TIMESTAMP;
```
Result:
```
+---------------------+
| Current_Timestamp   |
+---------------------+
| 2009-11-12 06:40:23 |
+---------------------+
1 row in set (0.00 sec)
```

---

Date Expressions: GETDATE() (SQL Server)
```sql
SELECT GETDATE();
```
Result:
```
+-------------------------+
| GETDATE                 |
+-------------------------+
| 2009-10-22 12:07:18.140 |
+-------------------------+
1 row in set (0.00 sec)
```

## Using Joins
A note taking from [SQL Tutorial - Using Joins](https://www.tutorialspoint.com/sql/sql-using-joins.htm)

The SQL Joins clause is used to combine records from two or more tables in a database. 
```sql
SELECT ID, NAME, AGE, AMOUNT
FROM CUSTOMERS, ORDERS
WHERE  CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```
- [INNER JOIN(EQUIJOIN)](https://www.tutorialspoint.com/sql/sql-inner-joins.htm): returns rows when there is a match in both tables
- [LEFT JOIN](https://www.tutorialspoint.com/sql/sql-left-joins.htm): returns all rows from the left table, even if there are no matches in the right table
- [RIGHT JOIN](https://www.tutorialspoint.com/sql/sql-right-joins.htm): returns all rows from the right table, even if there are no matches in the left table.
- [FULL JOINS](https://www.tutorialspoint.com/sql/sql-full-joins.htm): returns rows when there is a match in one of the tables
  ```sql
  SELECT  ID, NAME, AMOUNT, DATE
  FROM CUSTOMERS
  FULL JOIN ORDERS
  ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
  ```
  For RDBMSs do not support FULL JOIN (eg. mySQL): Use `UNION ALL`
  ```sql
  SELECT  ID, NAME, AMOUNT, DATE
    FROM CUSTOMERS
    LEFT JOIN ORDERS
    ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID
  UNION ALL
  SELECT  ID, NAME, AMOUNT, DATE
    FROM CUSTOMERS
    RIGHT JOIN ORDERS
    ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID
  ```
- [SELF JOIN](https://www.tutorialspoint.com/sql/sql-self-joins.htm): used to join a table to itself
- [CARTESIAN / CROSS JOINS](https://www.tutorialspoint.com/sql/sql-cartesian-joins.htm): returns the Cartesian product of the sets of records from the two or more joined tables

## UNIONS CLAUSE
A note taking from [SQL Tutorial - UNIONS Clause](https://www.tutorialspoint.com/sql/sql-unions-clause.htm)

The SQL UNION clause/operator is used to combine the results of two or more SELECT statements without returning duplicate rows.

To use UNION clause, each SELECT statement must have:
- The same number of columns selected and in same order
- The same number of column expressions
- The same data type

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

### INTERSECT Clause
A note taking from [[SQL Tutorial - INTERSECT Clause](https://www.tutorialspoint.com/sql/sql-intersect-clause.htm)

It is used to combine two SELECT statements, but returns rows only from the first SELECT statement that are identical to a row in the second SELECT statement.

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

INTERSECT

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

### EXCEPT Clause
A note taking from [SQL Tutorial - EXCEPT Clause](https://www.tutorialspoint.com/sql/sql-except-clause.htm)

It is used to combine two SELECT statements and returns rows from the first SELECT statement that are not returned by the second SELECT statement.<br>
This means EXCEPT returns only rows not available in the second SELECT statement.

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

EXCEPT

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

FYR: [【SQL入門】INTERSECT,EXCEPT,UNIONの違いを図解](https://gotto50105010.hatenablog.com/entry/2018/12/06/223615)

## Having Clause
A note taking from [SQL Tutorial - Having Clause](https://www.tutorialspoint.com/sql/sql-having-clause.htm)

The HAVING clause must follow the GROUP BY clause in a query and must also precede the ORDER BY clause if used.
```sql
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2
```

FYR: [【SQL】一目でわかる!HAVINGとWHEREの違いと活用方法](https://www.sejuku.net/blog/73003)

## NULL Values
A note taking from [SQL Tutorial - NULL Values](https://www.tutorialspoint.com/sql/sql-null-values.htm)

The SQL NULL is the term used to represent a missing value. 
A NULL value in a table is a value in a field that appears to be blank.

Example: set NOT NULL
```sql
CREATE TABLE CUSTOMERS(
   ID   INT              NOT NULL,
   NAME VARCHAR (20)     NOT NULL,
   AGE  INT              NOT NULL,
   ADDRESS  CHAR (25) ,
   SALARY   DECIMAL (18, 2),       
   PRIMARY KEY (ID)
);
```

Example: select rows with NULL / NOT NULL values
```sql
# Select rows with salary not null
SELECT  ID, NAME, AGE, ADDRESS, SALARY
FROM CUSTOMERS
WHERE SALARY IS NOT NULL;

# Select rows with salary null
SELECT  ID, NAME, AGE, ADDRESS, SALARY
FROM CUSTOMERS
WHERE SALARY IS NULL;
```

## Alias Syntax
A note taking from [SQL Tutorial - Alias Syntax](https://www.tutorialspoint.com/sql/sql-alias-syntax.htm)

```sql
# Table Alias
SELECT column1, column2....
FROM table_name AS alias_name
WHERE [condition];

# Column Alias
SELECT column_name AS alias_name
FROM table_name
WHERE [condition];
```

## Index
A note taking from [SQL Tutorial - Indexes](https://www.tutorialspoint.com/sql/sql-indexes.htm)

Indexes are special lookup tables that the database search engine can use to speed up data retrieval.<br>
An index helps to speed up SELECT queries and WHERE clauses, but it slows down data input, with the UPDATE and the INSERT statements.

Situations that the use of an index should be reconsidered:
- should not be used on small tables
- Tables that have frequent, large batch updates or insert operations
- Columns that contain a high number of NULL values
- Columns that are frequently manipulated

## Using Views
A note taking from [SQL Tutorial - Using Views](https://www.tutorialspoint.com/sql/sql-using-views.htm)

A view is a composition of a table in the form of a predefined SQL query.

Views are virtual tables allow users to:
- Structure data
- Restrict access to the data
- Summarize data from various tables to generate reports

### Creating Views
```sql
CREATE VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

#### WITH CHECK OPTION
To ensure that all UPDATE and INSERTs satisfy the conditions in the view

```sql
CREATE VIEW CUSTOMERS_VIEW AS
SELECT name, age
FROM  CUSTOMERS
WHERE age IS NOT NULL
WITH CHECK OPTION;
```

### Updating / Inserting rows to Views
A view can be updated under certain conditions:
- The SELECT clause may not contain keyword DISTINCT.
- The SELECT clause may not contain summary(aggregate) functions.
- The SELECT clause may not contain [set functions](https://www.dummies.com/programming/sql/sql-set-functions-2/).
- The SELECT clause may not contain [set operators](https://www.studytonight.com/dbms/set-operation-in-sql.php).
- The SELECT clause may not contain an ORDER BY clause.
- The FROM clause may not contain multiple tables.
- The query may not contain GROUP BY or HAVING.
- Calculated columns may not be updated.
- All NOT NULL columns from the base table must be included in the view.

```sql
UPDATE CUSTOMERS_VIEW
SET AGE = 35
WHERE name = 'Ramesh';
```

### Deleting rows from Views
The rules applied to UPDATE and INSERT commands also apply to the DELETE command.

### Dropping Views
```sql
DROP VIEW view_name;
```

## Transactions
A note taking from [SQL Tutorial - Transactions](https://www.tutorialspoint.com/sql/sql-transactions.htm)

Transactions are units or sequences of work accomplished in a logical order.

Standard properties for Transactions (ACID):
- Atomicity: ensures that all operations within the work unit are completed successfully (rollback when failed)
- Consistency: ensures that the database properly changes states upon a successfully committed transaction
- Isolation: enables transactions to operate independently
- Durability: ensures that the result or effect of a committed transaction persists in case of a system failure

FYR: [「トランザクションのACID特性」を理解する](https://www.atmarkit.co.jp/ait/articles/1703/01/news194.html)

### Transaction Control
- COMMIT: save the changes
- ROLLBACK: discard the changes
- SAVEPOINT: creates points within the groups of transactions
  ```sql
  # set SAVEPOINT
  SAVEPOINT SAVEPOINT_NAME;

  # rollback to SAVEPOINT
  ROLLBACK TO SAVEPOINT_NAME;

  # remove SAVEPOINT
  RELEASE SAVEPOINT SAVEPOINT_NAME;
  ```
- SET TRANSACTION: used to initiate a database transaction
  > **BEGIN** あるいは **START TRANSACTION** で目的の **transaction_modes** を指定すれば、 **SET TRANSACTION** は不要です。<br>
  > -- [SET TRANSACTION](https://www.postgresql.jp/document/9.0/html/sql-set-transaction.html) 

## Date Functions
Skipped [SQL Tutorial - Date Functions](https://www.tutorialspoint.com/sql/sql-date-functions.htm)

## Temporary Tables
A note taking from [SQL Tutorial - Temporary Tables](https://www.tutorialspoint.com/sql/sql-temporary-tables.htm)

It is used to store and process intermediate results and will be deleted when the current client session terminates.

- MySQL >= 3.23: Temporary Tables 
- MySQL < 3.23: Heap Tables

## Clone Tables
A note taking from [SQL Tutorial - Clone Tables](https://www.tutorialspoint.com/sql/sql-clone-tables.htm)

Steps:
1. Use `SHOW CREATE TABLE` command to get a CREATE TABLE statement specifies the source table's structure
   ```sql
   SHOW CREATE TABLE TUTORIALS_TBL

   /* Table: TUTORIALS_TBL
    * Create Table: CREATE TABLE 'TUTORIALS_TBL' ( 
    * 'tutorial_id' int(11) NOT NULL auto_increment, 
    * 'tutorial_title' varchar(100) NOT NULL default '', 
    * 'tutorial_author' varchar(40) NOT NULL default '', 
    * 'submission_date' date default NULL, 
    * PRIMARY KEY  ('tutorial_id'), 
    * UNIQUE KEY 'AUTHOR_INDEX' ('tutorial_author') 
    * ) TYPE = MyISAM    
    */
   ```
2. Change the table name in the statement to create a clone table
   ```sql
   CREATE TABLE 'CLONE_TBL' ( 
       'tutorial_id' int(11) NOT NULL auto_increment, 
       'tutorial_title' varchar(100) NOT NULL default '', 
       'tutorial_author' varchar(40) NOT NULL default '', 
       'submission_date' date default NULL, 
       PRIMARY KEY  ('tutorial_id'), 
       UNIQUE KEY 'AUTHOR_INDEX' ('tutorial_author') 
       ) TYPE = MyISAM; 
   ```
3. Copy the table contents by INSERT INTO or a SELECT statement
   ```sql
   INSERT INTO CLONE_TBL (tutorial_id, 
                          tutorial_title,
                          tutorial_author, 
                          submission_date) 
   SELECT tutorial_id,tutorial_title, tutorial_author, submission_date, 
   FROM TUTORIALS_TBL; 
   ```

## Sub Queries
A note taking from [SQL Tutorial - Sub Queries](https://www.tutorialspoint.com/sql/sql-sub-queries.htm)

A Subquery (Inner query / Nested query) is a query within another SQL query and embedded within the WHERE clause.

- Subqueries must be enclosed within parentheses.
- A subquery can have only one column in the SELECT clause
- `ORDER BY` command cannot be used in a subquery (GROUP BY command is okay)
- Subqueries that return multiple rows can only be used with multiple value operators (eg. IN operator)
- cannot include references to BLOB, ARRAY, CLOB, or NCLOB
- BETWEEN operator cannot be used with a subquery (but can be used within the subquery)

```sql
SELECT * 
FROM CUSTOMERS 
WHERE ID IN (SELECT ID 
             FROM CUSTOMERS
             WHERE SALARY > 4500) ;
```

## Using Sequences
A note taking from [SQL Tutorial - Using Sequences](https://www.tutorialspoint.com/sql/sql-using-sequences.htm)

### AUTO_INCREMENT
```sql
CREATE TABLE INSECT (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (id),
    name VARCHAR(30) NOT NULL,
    date DATE NOT NULL,
    origin VARCHAR(30) NOT NULL
);
```

### Obtain AUTO_INCREMENT Value
- SQL: use `LAST_INSERT_ID()` to get the auto-incremented value of last record
- PHP: call `mysql_insert_id()`
  ```php
  mysql_query ("INSERT INTO INSECT (name, date, origin)
                VALUES('moth','2001-09-14','windowsill')", $conn_id);
  $seq = mysql_insert_id ($conn_id);
  ```

### Renumbering an Existing Sequence
Be careful of this action and check if the table is having a join with another table

Renumber the sequence by drop the column and add it back again:
```sql
ALTER TABLE INSECT DROP id;

ALTER TABLE insect
ADD id INT UNSIGNED NOT NULL AUTO_INCREMENT FIRST,
ADD PRIMARY KEY (id);
```

### Starting Sequence at a Particular Value
```php
ALTER TABLE t AUTO_INCREMENT = 100;
```

## Injection
A note taking from [SQL Tutorial - Injection](https://www.tutorialspoint.com/sql/sql-injection.htm)

`mysql_query()` function does not permit query stacking or executing multiple SQL queries in a single function call.

Prevent SQL Injection:
- Pattern Matching
- mysql_real_escape_string()
  ```php
  $name = mysql_real_escape_string($name);
  mysql_query("SELECT * FROM CUSTOMERS WHERE name='{$name}'");
  ```
- For LIKE Quandary: use `addcslashes()` to specify a character range to escape.
  ```php
  $sub = addcslashes(mysql_real_escape_string("%str"), "%_");
  mysql_query("SELECT * FROM messages 
               WHERE subject LIKE '{$sub}%'");
  ```

## Database Tunning
A note taking from [SQL Tutorial - Database Tunning](https://www.tutorialspoint.com/sql/sql-database-tuning.htm)

- Use 3BNF database design
- Avoid number-to-character conversions
- For SELECT statements, only fetch required information (avoid using \*)
- Create indexes carefully. Avoid index on the tables for less SELECT and more INSERT / UPDATE operations
- Avoid full-table scans by creating an index on columns that are used as SELECT conditions
- Be careful of equality operators with real numbers and date/time values
- Use pattern matching judiciously
- Fine tune and examine the SQL queries and subqueries to discover whether the tables are well-designed
- Use procedures for queries that are executed on a regular basis. Unlike an SQL statement, the database engine need not optimize the procedure before execution
- Avoid using operator OR in a query if possible
- Avoid creating COMMIT after every record creation when performing batch transactions
- Defragment the database on a regular basis
