## Joins

Joins:

- Right, Left, Inner and Outer Join + Self Join

- Inner Join: returns only the matching rows from both tables.

```sql
SELECT * FROM table1 INNER JOIN table2 ON table1.column = table2.column;
```

- Left Join: returns all the rows from the left table and matching rows from the right table.

```sql
  SELECT * FROM table1 LEFT JOIN table2 ON table1.column = table2.column;
```

- Right Join: returns all the rows from the right table and matching rows from the left table.
```sql
  SELECT * FROM table1 RIGHT JOIN table2 ON table1.column = table2.column;
```

- Outer Join: returns all the rows from both tables, with NULL values for non-matching rows.
```sql
  SELECT * FROM table1 OUTER JOIN table2 ON table1.column = table2.column;
```

- Self Join: joins a table to itself using an alias.
```sql
  SELECT * FROM table1 AS t1 INNER JOIN table1 AS t2 ON t1.column = t2.column;
```

- USING clause: when both tables have the same connecting column name.
```sql
  SELECT * FROM table1 INNER JOIN table2 USING (column);
```

- Cartesian Product: join without a condition, returns every row from the first table matched with every row from the second table.
```sql
  SELECT * FROM table1, table2;
```

**Example:**

```sql
SELECT * FROM Actors INNER JOIN DigitalAssets ON Actors.Id = DigitalAssets.Id;
SELECT * FROM Actors LEFT JOIN DigitalAssets ON Actors.Id = DigitalAssets.Id;
SELECT * FROM Actors RIGHT JOIN DigitalAssets ON Actors.Id = DigitalAssets.Id;
SELECT FirstName, DoB, URL FROM Actors LEFT JOIN DigitalAssets USING(Id);
```

**SELF JOIN:**
- A table is joined with itself using an alias to compare values in a column to other values in the same column.
- Used to find hierarchical relationships between rows in a single table.

Example:
```sql  
SELECT t1.employee_name, t2.employee_name AS manager_name FROM table1 AS t1 INNER JOIN table1 AS t2 ON t1.manager_id = t2.id;
```