## Introduction to Databases & DBMS

> One of the most important skill for a Software Developer.

**A brief introduction to databases**

- A database is an organized collection of data that can be easily accessed, managed, and updated. Databases are used in various applications, such as websites, inventory systems, and banking systems.
- In a database, data is organized into tables, each of which consists of columns and rows. Each column represents a particular data type, while each row represents a specific instance of that data type.
- Databases can be managed using a database management system (DBMS), which provides tools for organizing, storing, and retrieving data. Examples of popular DBMSs include MySQL, Oracle, Microsoft SQL Server, and PostgreSQL.
- Databases can be classified into different types, such as relational databases, object-oriented databases, NoSQL databases, and graph databases. The choice of database type depends on the specific requirements of the application.

- Problems with File Based System
	1. Limited Scalability: File-based systems work well for smaller projects or applications but become increasingly difficult to manage as the project size grows. As the number of files increases, it becomes harder to organize, search and retrieve specific files.
	2. Data Duplication: With file-based systems, data is often stored in multiple places, leading to duplication and inconsistency. This can lead to errors and make it harder to maintain data integrity.
	3. Security Risks: File-based systems are prone to security risks such as unauthorized access, tampering or deletion of files. It can also be challenging to monitor who accesses which files and when.
	4. Inefficient Access: File-based systems

To get around the the above problems, We have "Databases".

- What is a database?
	- A database is _an organized collection of structured information, or data, typically stored electronically in a computer system_.
	- 
**DBMS**
- What is a DBMS?
	- DBMS stands for Database Management System. It is a software system that allows users to create, manage, and access databases.
	- It provides an interface between the user and the database, allowing users to interact with the data stored in the database by running queries or performing other operations. 
	- DBMS typically includes tools for backup and recovery, security management, performance monitoring, and optimization. 
	- Some examples of popular DBMS include Oracle, MySQL, Microsoft SQL Server, MongoDB, and PostgreSQL.
- What is the role of DBMS?
	- DBMS (Database Management System) is a software system that manages the storage, retrieval, and organization of data in a database. 
	- It acts as an intermediary between the user and the database, allowing users to create, modify, and delete data from the database while maintaining data integrity, security, and consistency. 
	- The role of DBMS is crucial in ensuring that data is organized efficiently and can be accessed quickly. 
	- It helps to eliminate data redundancy and ensures that data is consistent across all applications that use it. 
	- DBMS also provides a platform for managing transactions and enables multiple users to access the same data simultaneously without interfering with each other's work.
- SQL - (SQL stands for Structured Query Language)
	- SQL is a standard language for accessing and manipulating databases.
	- Although SQL is an ANSI/ISO standard, there are different versions of the SQL language.
- RDBMS
	- RDBMS stands for Relational Database Management System.
	- RDBMS is the basis for SQL, and for all modern database systems such as MS SQL Server, IBM DB2, Oracle, MySQL, and Microsoft Access.
	- The data in RDBMS is stored in database objects called tables. A table is a collection of related data entries and it consists of columns and rows.