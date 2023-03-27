## Normalisation - II

- What is Normalisation?
	- It is the process of determining how much redundancy exist in a table & it gives us techniques to reduce it.
- Normal Forms
	- 1NF
	- 2NF
	- 2NF
	- BCNF
- First Normal Form / 1NF
	- Any attribute must only contain atomic values.
- Second Normal Form / 2NF
	- The table should not have partial dependency, and the table should be already in 1NF.
- 3rd Normal Form / 3NF
	- The table should be in 2ND and it should not have transitive dependency.

### What is Normalization?

Normalization is the process of determining how much redundancy exists in a table and provides techniques to reduce it. For example, if you have a table with columns for `order_id`, `customer_id`, `customer_name`, and `order_date`, there might be redundancy because the same customer name is repeated for each order by that customer. Normalization can help you reduce this redundancy by splitting the data into two tables: one for orders and one for customers.

  **Normal Forms include 1NF, 2NF, 3NF, and BCNF.**

-   **First Normal Form (1NF)** requires that any attribute must only contain atomic values. For example, if you have a table with a column for `phone_numbers`, it should not contain multiple phone numbers separated by commas. Instead, each phone number should be stored in a separate row.
-   **Second Normal Form (2NF)** requires that the table should not have partial dependency and should already be in 1NF. For example, if you have a table with columns for `order_id`, `customer_id`, `customer_name`, and `order_date`, there is a partial dependency because the `customer_name` column depends only on the `customer_id` column and not on the whole primary key (`order_id`, `customer_id`). To bring the table to 2NF, you could split it into two tables: one for orders and one for customers.
-   **Third Normal Form (3NF)** requires that the table should be in 2NF and should not have transitive dependency. For example, if you have a table with columns for `order_id`, `customer_id`, `customer_address`, and `order_date`, there is a transitive dependency because the `customer_address` column depends on the `customer_id` column which in turn depends on the primary key (`order_id`). To bring the table to 3NF, you could split it into two tables: one for orders and one for customers.


Example -  a database for an online store that sells books. We have information about customers, orders, and books.

We can start by creating three tables: `Customers`, `Orders`, and `Books`.

The `Customers` table could have columns for `customer_id` (primary key), `first_name`, `last_name`, and `email`. This table is in 1NF because all attributes contain atomic values. It’s also in 2NF and 3NF because there are no partial or transitive dependencies.

The `Orders` table could have columns for `order_id` (primary key), `customer_id` (foreign key), and `order_date`. This table is also in 1NF, 2NF, and 3NF.

The `Books` table could have columns for `book_id` (primary key), `title`, `author`, and `price`. This table is also in 1NF, 2NF, and 3NF.

To represent the relationship between orders and books (i.e., which books are included in each order), we could create another table called `Order_Items` with columns for `order_id` (foreign key), `book_id` (foreign key), and `quantity`. This table is also in 1NF, 2NF, and 3NF.

Sure! Here are some markdown tables with dummy data for the tables you provided:

**Customers**

| customer_id | first_name | last_name | email |
|-------------|------------|-----------|-------|
| 1           | Alice      | Smith     | alice@example.com |
| 2           | Bob        | Johnson   | bob@example.com |
| 3           | Charlie    | Williams  | charlie@example.com |

**Orders**

| order_id | customer_id | order_date |
|----------|-------------|------------|
| 1        | 1           | 2022-01-01 |
| 2        | 2           | 2022-01-02 |
| 3        | 3           | 2022-01-03 |

**Books**

| book_id | title       | author     | price |
|---------|-------------|------------|-------|
| 1       | Book A      | Author A   | 10.99 |
| 2       | Book B      | Author B   | 12.99 |
| 3       | Book C      | Author C   | 14.99 |

**Order_Items**

| order_id | book_id | quantity |
|----------|---------|----------|
| 1        | 1       | 1        |
| 1        | 2       | 2        |
| 2        | 3       | 1        |
| 3        | 1       | 1        |
| 3        | 2       | 1        |
| 3        | 3       | 1        |


```sql
CREATE TABLE Customers (
    customer_id INTEGER PRIMARY KEY,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT NOT NULL
);

CREATE TABLE Orders (
    order_id INTEGER PRIMARY KEY,
    customer_id INTEGER NOT NULL,
    order_date DATE NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE Books (
    book_id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    author TEXT NOT NULL,
    price NUMERIC NOT NULL
);

CREATE TABLE Order_Items (
    order_id INTEGER NOT NULL,
    book_id INTEGER NOT NULL,
    quantity INTEGER NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (book_id) REFERENCES Books(book_id)
);
```

## Normalisation - III & Design Schema

### Design a db for a Quora like app

- User should be able to post a question
- User should be able to answer a question
- User should be able to comment on an answer
- User should be able to comment on a comment
- User should be able to like a comment or a question or an answer
- User should be able to follow another user
- Every question can belong to multiple topics
- User can follow a topic also
- You should be able to filter out questions based on topic


#### Users Table
| Column Name | Data Type |
| --- | --- |
| user\_id | integer |
| username | varchar |
| email | varchar |
| ... | ... |

#### Questions Table
| Column Name | Data Type |
| --- | --- |
| question\_id | integer |
| user\_id | integer |
| title | varchar |
| description | text |
| created\_at | timestamp |
| ... | ... |

#### Answers Table
| Column Name | Data Type |
| --- | --- |
| answer\_id | integer |
| question\_id | integer |
| user\_id | integer |
| content | text |
| created\_at | timestamp |
| ... | ... |

#### Comments Table
| Column Name | Data Type |
| --- | --- |
| comment\_id | integer |
| parent\_id | integer |
| user\_id | integer |
| content | text |
| created\_at | timestamp |
| ... | ... |

#### Likes Table
| Column Name | Data Type |
| --- | --- |
| like\_id | integer |
| user\_id | integer |
| content\_type | varchar |
| content\_id | integer |
| ... | ... |

#### Follows Table
| Column Name | Data Type |
| --- | --- |
| follow\_id | integer |
| follower\_id | integer |
| followee\_id | integer |
| ... | ... |

#### Topics Table
| Column Name | Data Type |
| --- | --- |
| topic\_id | integer |
| name | varchar |
| ... | ... |

#### QuestionTopics Table
| Column Name | Data Type |
| --- | --- |
| question\_topic\_id | integer |
| question\_id | integer |
| topic\_id | integer |
| ... | ... |

#### TopicFollows Table
| Column Name | Data Type |
| --- | --- |
| topic\_follow\_id | integer |
| user\_id | integer |
| topic\_id | integer |
| ... | ... |

### Prime attribute & Non prime attribute

In the context of database design and normalization, a **prime attribute** is an attribute that is part of a candidate key of a relation. A **non-prime attribute** is an attribute that is not part of any candidate key.

For example, let’s say we have a relation `Student` with attributes `student_id`, `name`, `email`, and `phone`. Let’s assume that `student_id` is unique for each student and can be used to identify a student. In this case, `student_id` is a candidate key for the relation `Student`. Since `student_id` is part of a candidate key, it is a prime attribute. The other attributes (`name`, `email`, and `phone`) are not part of any candidate key and are therefore non-prime attributes.

In summary, prime attributes are part of candidate keys and can be used to uniquely identify a tuple in a relation. Non-prime attributes are not part of any candidate key and cannot be used to uniquely identify a tuple.

### BCNF form

BCNF (Boyce-Codd Normal Form) is an advanced version of the Third Normal Form (3NF). A table is in BCNF if for every functional dependency X → Y, X is the super key of the table1.

Here’s an example to help you understand BCNF better:

Consider a relation R(A, B, C) with functional dependencies {A → BC, B → A}. In this relation, both A and B are super keys so the relation is in BCNF

Sure! Here's an example with tables to help you understand BCNF better:

Consider a college enrollment table with columns `student_id`, `subject`, and `professor`.

| student_id | subject | professor |
|------------|---------|-----------|
| 101        | Java    | P.Java    |
| 101        | C++     | P.Cpp     |
| 102        | Java    | P.Java2   |
| 103        | C#      | P.Chash   |
| 104        | Java    | P.Java    |

In this table, one student can enroll for multiple subjects. For each subject, a professor is assigned to the student. And there can be multiple professors teaching one subject like we have for Java.

`student_id` and `subject` together form the primary key because using `student_id` and `subject`, we can find all the columns of the table. One professor teaches only one subject, but one subject may have two different professors. Hence, there is a dependency between `subject` and `professor` here, where `subject` depends on the professor's name.

This table satisfies the 1st Normal Form because all the values are atomic, column names are unique and all the values stored in a particular column are of the same domain. This table also satisfies the 2nd Normal Form as there is no Partial Dependency. And there is no Transitive Dependency, hence the table also satisfies the 3rd Normal Form.

But this table is not in Boyce-Codd Normal Form (BCNF). In the table above, `student_id` and `subject` form the primary key, which means the `subject` column is a prime attribute. But there is one more dependency: `professor → subject`. And while `subject` is a prime attribute, `professor` is a non-prime attribute, which is not allowed by BCNF.

To make this relation (table) satisfy BCNF, we will decompose this table into two tables: a student table and a professor table.

Sure! To make the college enrollment table satisfy BCNF, we will decompose it into two tables: a student table and a professor table.

The student table will have columns `student_id` and `subject`:

| student_id | subject |
|------------|---------|
| 101        | Java    |
| 101        | C++     |
| 102        | Java    |
| 103        | C#      |
| 104        | Java    |

The professor table will have columns `subject` and `professor`:

| subject | professor |
|---------|-----------|
| Java    | P.Java    |
| C++     | P.Cpp     |
| Java    | P.Java2   |
| C#      | P.Chash   |

Now both tables are in BCNF. 

### 4NF

Fourth Normal Form (4NF) is an extension of Boyce-Codd Normal Form (BCNF). A relation is in 4NF if it satisfies the following conditions:
- It is in BCNF.
- It does not have any multi-valued dependency².

A multi-valued dependency occurs when an attribute depends on another attribute, but not on the key of the relation. For a multi-valued dependency to occur there must be at least 3 columns in the relation. If X → Y exists in a relation R (X, Y, Z) then Y and Z should be independent of each other².

Here's an example to help you understand 4NF better:

Consider a table `Student` that contains the following records:

| Stu_Id | Stu_Course | Stu_Hobby |
|--------|------------|-----------|
| 101    | C++        | Reading   |
| 101    | C++        | Writing   |

A student can join more than one course. Let's say student with `Stu_Id` 101 joins another course "Java". For a new course, instead of one row, two rows need to be added.

| Stu_Id | Stu_Course | Stu_Hobby |
|--------|------------|-----------|
| 101    | C++        | Reading   |
| 101    | C++        | Writing   |
| 101    | Java       | Reading   |
| 101    | Java       | Writing   |

This relation has a multi-valued dependency as `Stu_Id → Stu_Course`, meaning student course is dependent on student id and for each student id, there are multiple courses. Also, `Stu_Course` and `Stu_Hobby` are independent of each other.

This table has unnecessary records as for each new course, more than one record needs to be added to the table. Let's decompose this table into 4NF:

This table is already in BCNF. We just need to remove the multi-valued dependency. Let's decompose the original table into two tables.

Table 1: Course

| Stu_Id | Stu_Course |
|--------|------------|
| 101    | C++        |
| 101    | Java       |

Table 2: Hobby

| Stu_Id | Stu_Hobby |
|--------|-----------|
| 101    | Reading   |
| 101    | Writing   |

These tables are in 4NF as the multi-valued dependency is removed².

### Design a database for a Quora-like app

-   User should be able to post a question
-   User should be able to answer a question
-   User should be able to comment on an answer
-   User should be able to comment on a comment
-   User should be able to like a comment or a question or an answer
-   User should be able to follow another user
-   Every question can belong to multiple topics
-   User can follow a topic also
-   You should be able to filter out questions based on topic

#### Users Table
| Column Name | Data Type |
| --- | --- |
| user\_id | integer |
| username | varchar |
| email | varchar |
| ... | ... |


#### Questions Table

| Column Name | Data Type |
| --- | --- |
| question\_id | integer |
| user\_id | integer |
| title | varchar |
| description | text |
| created\_at | timestamp |
| ... | ... |


#### Answers Table

| Column Name | Data Type |
| --- | --- |
| answer\_id | integer |
| question\_id | integer |
| user\_id | integer |
| content | text |
| created\_at | timestamp |
| ... | ... |


#### Comments Table

| Column Name | Data Type |
| --- | --- |
| comment\_id | integer |
| parent\_id | integer |
| user\_id | integer |
| content | text |
| created\_at | timestamp |
| ... | ... |


#### Likes Table


| Column Name | Data Type |
| --- | --- |
| like\_id | integer |
| user\_id | integer |
| content\_type | varchar |
| content\_id | integer |
| ... | ... |


#### Follows Table


| Column Name | Data Type |
| --- | --- |
| follow\_id | integer |
| follower\_id | integer |
| followee\_id | integer |
| ... | ... |


#### Topics Table


| Column Name | Data Type |
| --- | --- |
| topic\_id | integer |
| name | varchar |
| ... | ... |


#### QuestionTopics Table


| Column Name | Data Type |
| --- | --- |
| question\_topic\_id | integer |
| question\_id | integer |
| topic\_id | integer |
| ... | ... |


#### TopicFollows Table


| Column Name | Data Type |
| --- | --- |
| topic\_follow\_id | integer |
| user\_id | integer |
| topic\_id | integer |
| ... | ... |

### Design a movie booking application DB

-   User can book a movie on a particular theatre and timings
-   User can review a movie
-   User can review a theatre
-   Review involves rating and comments both
-   You can cancel a booking / or if your payment is stuck then booking can be pending else confirmed
-   You should be able to get all movies from a theatre
-   You should be able to get all theatres of a particular city where movie is running


#### Users

A table to store user information such as user\_id, name, email, password, etc.

| Column Name | Data Type | Description |
| --- | --- | --- |
| user\_id | integer | Unique identifier for each user |
| name | varchar | User's name |
| email | varchar | User's email address |
| password | varchar | User's password |
| ... | ... | Other user information |


#### Theatres

A table to store theatre information such as theatre\_id, name, city, address, etc.

| Column Name | Data Type | Description |
| --- | --- | --- |
| theatre\_id | integer | Unique identifier for each theatre |
| name | varchar | Name of the theatre |
| city | varchar | City where the theatre is located |
| address | varchar | Address of the theatre |
| ... | ... | Other theatre information |


#### Movies

A table to store movie information such as movie\_id, name, description, etc.

| Column Name | Data Type | Description |
| --- | --- | --- |
| movie\_id | integer | Unique identifier for each movie |
| name | varchar | Name of the movie |
| description | varchar | Description of the movie |
| ... | ... | Other movie information |


#### TheatreMovies

A table to store the relationship between theatres and movies with columns such as theatre\_movie\_id, theatre\_id, and movie\_id.

| Column Name | Data Type | Description |
| --- | --- | --- |
| theatre\_movie\_id | integer | Unique identifier for each theatre-movie relationship |
| theatre\_id | integer | Identifier for the theatre |
| movie\_id | integer | Identifier for the movie |
| ... | ... | Other theatre-movie relationship information |

#### Showtimes

A table to store showtime information such as showtime\_id, theatre\_id, movie\_id, start\_time, end\_time, etc.

| Column Name | Data Type | Description |
| --- | --- | --- |
| showtime\_id | integer | Unique identifier for each showtime |
| theatre\_id | integer | Identifier for the theatre |
| movie\_id | integer | Identifier for the movie |
| start\_time | datetime | Start time of the show |
| end\_time | datetime | End time of the show |
| ... | ... | Other showtime information |


#### Bookings

A table to store booking information such as booking\_id, user\_id, showtime\_id, status, payment\_status, etc.

| Column Name | Data Type | Description |
| --- | --- | --- |
| booking\_id | integer | Unique identifier for each booking |
| user\_id | integer | Identifier for the user |
| showtime\_id | integer | Identifier for the showtime |
| status | varchar | Status of the booking (confirmed, cancelled, pending) |
| payment\_status | varchar | Status of the payment (paid, pending) |
| ... | ... | Other booking information |


#### Reviews

A table to store review information such as review\_id, user\_id, movie\_id, theatre\_id, rating, comment, etc.
| Column Name | Data Type | Description |
| --- | --- | --- |
| review\_id | integer | Unique identifier for each review |
| user\_id | integer | Identifier for the user |
| movie\_id | integer | Identifier for the movie |
| theatre\_id | integer | Identifier for the theatre |
| rating | integer | Rating given by the user (out of 5) |
| comment | varchar | Comment given by the user |
| ... | ... | Other review information |


With this design, you can easily perform operations such as booking a movie, reviewing a movie or theatre, cancelling a booking, getting all movies from a theatre,

## Triggers & Joins (Lecture)

- A practical lecture, Please refer to live lecture.
- Refer this link for Database.
	- github.com/hhorak/mysql-sample-db/blob/master/mysqlsampledatabase.sql