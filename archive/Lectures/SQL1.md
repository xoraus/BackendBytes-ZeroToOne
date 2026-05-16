
## Lecture Notes: Introduction to SQL

SQL stands for Structured Query Language, and it is a language used to manage and manipulate relational databases. In this lecture, we will cover the basic SQL commands needed to create and manage databases and tables. We will also learn how to insert data into tables and retrieve data using SELECT statements.

### SQL Commands:

- This command is used to display all the available databases on the system.

```sql
SHOW DATABASES;
```

- This command is used to select a specific database to run queries on.

```sql
USE database_name;
```

- This command is used to list all the tables within the selected database.

```sql
SHOW TABLES;
```

- This command is used to create a new database.

```sql
CREATE DATABASE database_name;
```

- This command is used to create a new table within the selected database.

```sql
CREATE TABLE table_name (
column_name1 datatype,
column_name2 datatype,
...
);
```

- This command is used to show the structure and description of the specified table.

```sql
DESC table_name;
```

- This command is used to insert values into a specified table.

```sql
INSERT INTO table_name (col1, col2, ...) VALUES (value1, value2, ...);
```

- This command is used to retrieve specific column data from a specified table. Use * in place of column name to retrieve all the columns.

```sql
SELECT column_name FROM table_name;
```

- This command is used to retrieve data from a specified table based on specified conditions.

```sql
SELECT * FROM table_name WHERE conditions;
```

Examples:

```sql
SELECT * FROM second WHERE DoB > '2000-01-01' AND Gender = 'Female';
SELECT * FROM second ORDER BY DoB DESC;
SELECT * FROM second WHERE NOT Gender = 'Male’;
SELECT * FROM second WHERE DoB > '2020-01-03' OR Gender = 'Female';
SELECT * FROM second WHERE Name LIKE "_a%";
SELECT * FROM second WHERE Name LIKE "%a%";
SELECT * FROM second WHERE Gender = 'Female' ORDER BY DoB DESC LIMIT 2;
```

### SQL Tables:

In order to create tables, we use the CREATE TABLE command, followed by the table name, and then a list of column names and their data types. Each column can have a unique data type, such as varchar or integer. We can also specify other properties for columns, such as NOT NULL or UNIQUE.

Once we have created a table, we can insert data into it using the INSERT INTO command. We specify the table name, followed by the column names we want to insert data into, and then the values for those columns.

Finally, we can retrieve data from tables using SELECT statements. We can specify conditions using operators such as AND, OR, and NOT, as well as comparison operators such as >, <, and =. We can also sort data using the ORDER BY command, and limit the amount of data returned using the LIMIT command.