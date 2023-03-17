# Topic: SQL Table Operations

## Introduction:

- SQL (Structured Query Language) is a programming language used for managing relational databases.
- It provides various commands to perform operations on the database tables such as create, update, insert, delete, and select.

I. Basic Commands:

A. Show Database

SHOW DATABASES; - to show all the databases available on your system.
B. Select Database

USE [database_name]; - to select a particular database to run queries on.
C. Show Tables

SHOW TABLES; - to list down all the tables of a particular selected database.
D. Create Database

CREATE DATABASE [database_name]; - to create a new database.
E. Create Table

CREATE TABLE [table_name] (
[column_name_1] [datatype_1],
[column_name_2] [datatype_2],
...
); - to create a new table.
F. Describe Table

DESC [table_name]; - to show the structure and description of the table.
G. Insert Values

INSERT INTO [table_name] ([column_name_1], [column_name_2], ...)
VALUES ([value_1], [value_2], ...); - to insert values into a table.
H. Select Data

SELECT [column_name] FROM [table_name]; - to get specific column data from a table.
SELECT * FROM [table_name]; - to get all the columns.
SELECT [column_name] FROM [table_name] WHERE [conditions]; - to get filtered data based on the given conditions.
II. Advanced Commands:
A. Delete Rows

DELETE FROM [table_name] WHERE [conditions] ORDER BY [column_name] LIMIT [number]; - to delete rows from a table based on certain conditions.
B. Update Rows

UPDATE [table_name] SET [column_name_1] = [value_1] WHERE [conditions]; - to update rows in a table based on certain conditions.
C. Alter Table

ALTER TABLE [table_name] CHANGE [old_column_name] [new_column_name] [datatype/constraint]; - to modify an existing column.
ALTER TABLE [table_name] ADD [column_name] [datatype/constraint]; - to add a new column to the table.
D. Aggregate Functions

SELECT function_name FROM [table_name]; - to perform calculations on columns.
Functions include SUM, AVG, COUNT, and DISTINCT.
E. Group By

SELECT [column_name], COUNT(*) FROM [table_name] GROUP BY [column_name]; - to group data based on a particular column.
F. Order By

SELECT [column_name] FROM [table_name] ORDER BY [column_name] [ASC/DESC]; - to sort data in ascending or descending order.
G. Limit

SELECT [column_name] FROM [table_name] LIMIT [number]; - to limit the number of rows returned by the query.
H. Concatenate Strings

SELECT CONCAT([column_name_1], ' ', [column_name_2]) AS [new_column_name] FROM [table_name]; - to combine two or more strings into a single column.
Conclusion:

SQL provides various commands to perform operations on tables in a database.
The basic commands include show database, select database, show tables, create database, create table, describe table, insert values, and select data.
The advanced commands include delete rows, update rows, alter table, aggregate functions, group by, order by, limit, and concatenate strings.