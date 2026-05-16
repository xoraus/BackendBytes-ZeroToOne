# Topic: SQL Table Operations

## Introduction:

- SQL (Structured Query Language) is a programming language used for managing relational databases.
- It provides various commands to perform operations on the database tables such as create, update, insert, delete, and select.

## I. Basic Commands:

A. Show Database - to show all the databases available on your system.

```sql
SHOW DATABASES;
```

B. Select Database - to select a particular database to run queries on.

```sql
USE [database_name];
```

C. Show Tables - to list down all the tables of a particular selected database.

```sql
SHOW TABLES;
```

D. Create Database - to create a new database.

```sql
CREATE DATABASE [database_name];
```

E. Create Table - to create a new table.
```sql
CREATE TABLE [table_name] (
[column_name_1] [datatype_1],
[column_name_2] [datatype_2],
...
); 
```

F. Describe Table - to show the structure and description of the table.
```sql
DESC [table_name];
```


G. Insert Values - to insert values into a table.
```sql
INSERT INTO [table_name] ([column_name_1], [column_name_2], ...)
VALUES ([value_1], [value_2], ...); 
```

H. Select Data

```sql
--- to get specific column data from a table.
SELECT [column_name] FROM [table_name]; 

-- to get all the columns.
SELECT * FROM [table_name]; 

-- to get filtered data based on the given conditions.
SELECT [column_name] FROM [table_name] WHERE [conditions]; 
```

## II. Advanced Commands:

A. Delete Rows - to delete rows from a table based on certain conditions.

```sql
DELETE FROM [table_name] WHERE [conditions] ORDER BY [column_name] LIMIT [number]; 
```


B. Update Rows - to update rows in a table based on certain conditions.
```sql
UPDATE [table_name] SET [column_name_1] = [value_1] WHERE [conditions];
```


C. Alter Table 
```sql
-- to modify an existing column.
ALTER TABLE [table_name] CHANGE [old_column_name] [new_column_name] [datatype/constraint]; 

-- to add a new column to the table.
ALTER TABLE [table_name] ADD [column_name] [datatype/constraint]; 
```

D. Aggregate Functions - to perform calculations on columns.
```sql
SELECT function_name FROM [table_name];
```

Functions include SUM, AVG, COUNT, and DISTINCT.

E. Group By - to group data based on a particular column.

```sql
SELECT [column_name], COUNT(*) FROM [table_name] GROUP BY [column_name]; 
```

F. Order By - to sort data in ascending or descending order.
```sql
SELECT [column_name] FROM [table_name] ORDER BY [column_name] [ASC/DESC]; 
```

G. Limit - to limit the number of rows returned by the query.
```sql
SELECT [column_name] FROM [table_name] LIMIT [number]; 
```

H. Concatenate Strings - to combine two or more strings into a single column.
```sql
SELECT CONCAT([column_name_1], ' ', [column_name_2]) AS [new_column_name] FROM [table_name]; 
```

**Conclusion:**

SQL provides various commands to perform operations on tables in a database.
The basic commands include show database, select database, show tables, create database, create table, describe table, insert values, and select data.
The advanced commands include delete rows, update rows, alter table, aggregate functions, group by, order by, limit, and concatenate strings.