
**What is Databases Schema** → blue print
- It is the blue print of actual db, that we will create.
- How we will store data, structures of table etc is defined in it.

**Databases Instance?**
- A database instance is a set of memory structures and background processes that manage database files. It includes multiple parts, including the relational database management system (RDBMS) software, table structure, stored procedures, and other functionality

**Functional Dependency.**
-  It’s like a relationship between two things where one thing can help you figure out the other thing.
- It defined relationship between two attributes.
- **X**(determined) → **Y**(dependent) (**Y** is functionally dependent on **X**)
- **Y** depends on **X**, means that for evert valid value of **X**, we can uniquely identify **Y**.

 **How can you identify functional dependency ?**   
- To identify functional dependencies, you need to examine the data and the business rules of the database. You can check if a functional dependency exists by asking yourself if the value of one attribute changes, does the value of another attribute change as well? Can the value of another attribute be determined if the value of one attribute is known?

 **Assertion**
- Assertions have been part of the SQL standard since SQL-92. They can be used to implement what’s commonly called cross-row constraints or multi-table check constraints.
- In the SQL standard, both assertions and check constraints are considered “constraints” which are rules that the data actually contained in the database must comply with.

For example, let’s say you’re writing a program that calculates the square root of a number. You might include an assertion that checks that the number you’re trying to find the square root of is not negative because you know that the square root of a negative number is not a real number.

example of how an assertion might be used in SQL:

Let's say you have two tables: `Orders` and `Customers`. The `Orders` table has a column for the customer ID and the `Customers` table has a column for the customer's credit limit. You want to make sure that no customer can place an order that exceeds their credit limit.

You could create an assertion that checks that the total value of all orders for each customer is less than or equal to their credit limit. The assertion would look something like this:

```sql
CREATE ASSERTION orders_within_credit_limit
CHECK (
    NOT EXISTS (
        SELECT 1
        FROM Customers c
        JOIN Orders o ON c.customer_id = o.customer_id
        GROUP BY c.customer_id, c.credit_limit
        HAVING SUM(o.order_value) > c.credit_limit
    )
);
```

This assertion checks that there are no customers for whom the total value of their orders is greater than their credit limit. If this condition is ever violated, the assertion will fail and an error will be raised.

### Axioms in DBMS

Axioms are a set of rules that define how the attributes in a relational database are functionally dependent on each other. In DBMS, there are three axioms: reflexivity, augmentation, and transitivity.

#### Reflexivity

The first axiom, reflexivity, states that if one attribute is a subset of another attribute, then the subset attribute is functionally dependent on the other attribute. For example, consider the attributes of an address: address, state, and house number. If the state is a subset of the address, then the state is functionally dependent on the address. This means that if you know the address, you can determine the state.

#### Augmentation

The second axiom, augmentation, states that if **X → Y**, then **X2 → Y2** for any attribute **Z**. This axiom leads to partial dependency. This means that if a non-key attribute is functionally dependent on a part of the primary key, then it is functionally dependent on the whole primary key. For example, suppose you have a table with the attributes of a person's name, address, and phone number. If the name is the primary key, and the phone number is functionally dependent on the address, then the phone number is partially dependent on the primary key.

#### Transitivity

The third axiom, transitivity, states that if **X → Y** and **Y → Z**, then **X → Z**. This axiom defines that if there is a functional dependency between X and Y, and there is a functional dependency between Y and Z, then there must be a functional dependency between X and Z. For example, suppose you have a table with the attributes of a person's name, address, and phone number. If the name is functionally dependent on the address, and the address is functionally dependent on the phone number, then the name is functionally dependent on the phone number.

### Database Keys

In a database, a key is a set of one or more columns that uniquely identifies a row in a table. There are several types of keys in a database, including primary keys, candidate keys, and foreign keys.

#### super key

A **super key** is a set of one or more columns that can be used to uniquely identify a row in a table. In other words, it’s a set of columns that contains a candidate key. A super key can have additional columns that are not necessary for unique identification.

For example, let’s say you have a table called `Employees` with columns for `employee_id`, `first_name`, `last_name`, and `email`. The `employee_id` column is the primary key and is also a candidate key. The set of columns `{employee_id, first_name}` is a super key because it contains the candidate key `employee_id` and can be used to uniquely identify each row. However, the `first_name` column is not necessary for unique identification because the `employee_id` column alone is sufficient.

#### candidate key

A **candidate key** is a minimal super key, meaning it’s a set of columns that can uniquely identify each row in a table and no subset of the columns can also uniquely identify each row. In other words, it’s a super key with no unnecessary columns.

For example, let’s say you have a table called `Customers` with columns for `customer_id`, `first_name`, `last_name`, and `email`. The `customer_id` column is the primary key and is also a candidate key because it can uniquely identify each row. The set of columns `{customer_id, first_name}` is a super key but not a candidate key because the `first_name` column is not necessary for unique identification.

A table can have multiple candidate keys. In our `Customers` example, if each customer also had a unique email address, the `email` column could be another candidate key.

#### composite key

A **composite key** is a key that consists of two or more columns. It’s used when a single column is not sufficient to uniquely identify each row in a table.

For example, let’s say you have a table called `Enrollments` with columns for `student_id`, `course_id`, and `enrollment_date`. In this case, neither the `student_id` nor the `course_id` column alone can uniquely identify each row because a student can enroll in multiple courses and a course can have multiple students. However, the combination of `student_id` and `course_id` can uniquely identify each row because each student can only enroll in a specific course once. So, the set of columns `{student_id, course_id}` is a composite key.

#### primary key

A **primary key** is a column or set of columns that uniquely identifies each row in a table. It’s a special type of candidate key that is chosen by the database designer to be the main way of identifying rows in the table.

For example, let’s say you have a table called `Students` with columns for `student_id`, `first_name`, and `last_name`. The `student_id` column could be the primary key because each student has a unique ID. This means that you can use the `student_id` column to look up a specific student in the table.

A primary key must be unique and cannot contain null values. It’s also usually indexed to improve the performance of queries that use it.

#### alternate key

An **alternate key** is a candidate key that is not the primary key. In other words, it’s a column or set of columns that can uniquely identify each row in a table but is not chosen as the primary key.

For example, let’s say you have a table called `Customers` with columns for `customer_id`, `first_name`, `last_name`, and `email`. The `customer_id` column is the primary key and is also a candidate key. If each customer also had a unique email address, the `email` column could be an alternate key because it can uniquely identify each row but is not the primary key.

Alternate keys are sometimes called secondary keys or non-prime keys.

#### foreign key

A **foreign key** is a column or set of columns in one table that refers to the primary key of another table. It’s used to create a relationship between the two tables and to enforce referential integrity.

For example, let’s say you have two tables: `Orders` and `Customers`. The `Orders` table has columns for `order_id`, `customer_id`, and `order_date`. The `Customers` table has columns for `customer_id`, `first_name`, and `last_name`. The `customer_id` column in the `Customers` table is the primary key.

In this case, the `customer_id` column in the `Orders` table could be a foreign key that refers to the `customer_id` primary key in the `Customers` table. This creates a relationship between the two tables where each order is associated with a specific customer. The database will enforce referential integrity by ensuring that any value entered into the `customer_id` column in the `Orders` table must match a value in the `customer_id` column in the `Customers` table.

#### Functional dependency Closure

In a database, the **closure** of a set of functional dependencies is the set of all functional dependencies that can be derived from the original set. In other words, it’s the set of all functional dependencies that must hold in any relation that satisfies the original set of functional dependencies.

Let me explain it with an example. Let’s say you have a relation with columns for `student_id`, `first_name`, `last_name`, and `email`. You know that each student has a unique ID and a unique email address. So, you have two functional dependencies: `student_id -> first_name` and `student_id -> last_name`.

The closure of this set of functional dependencies would include the original two functional dependencies as well as any other functional dependencies that can be derived from them. In this case, the closure would also include the functional dependency `student_id -> email` because if you know a student’s ID, you can also determine their email address.

#### Attribute Closure

The **attribute closure** of a set of attributes is the set of all attributes that are functionally dependent on the original set. In other words, it’s the set of all attributes that can be determined if you know the values of the original set of attributes.

Let me explain it with an example. Let’s say you have a relation with columns for `student_id`, `first_name`, `last_name`, and `email`. You know that each student has a unique ID and a unique email address. So, you have two functional dependencies: `student_id -> first_name` and `student_id -> last_name`.

The attribute closure of the set of attributes `{student_id}` would include the original attribute `student_id` as well as any other attributes that can be determined if you know the value of `student_id`. In this case, the attribute closure would also include the attributes `first_name` and `last_name` because if you know a student’s ID, you can also determine their first name and last name.

### N+1 problem

The **N+1 problem** is a performance issue that can happen when you’re working with databases. It happens when your code makes too many queries to the database, slowing down your program.

Let me explain it with an example. Let’s say you have a database with two tables: `Authors` and `Books`. The `Authors` table has columns for `author_id` and `name`. The `Books` table has columns for `book_id`, `title`, and `author_id`.

Now, imagine you want to write a program that displays a list of all authors and their books. You might write code that first queries the `Authors` table to get a list of all authors. Then, for each author, you make another query to the `Books` table to get a list of all their books.

This is where the N+1 problem comes in. If you have N authors, your code will make N+1 queries to the database: one query to get the list of authors and N queries to get the books for each author. This can be very slow if you have a lot of authors.

The solution to the N+1 problem is to use a technique called **eager loading**. Instead of making multiple queries to the database, you make one query that gets all the data you need at once. In our example, you could use a JOIN statement to get a list of all authors and their books with just one query.
