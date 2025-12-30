---
layout: post
title:  "An Intro to Relational Database and SQL"
date:   2025-12-29
---

**What is a relational database?**  
A relational database is a type of database, where data is stored in rows and columns, which collectively form multiple tables. Data from these tables can be linked using primary key or foreign keys. 

**What is a primary key?**  
A primary key is a column in a table that uniquely identifies each row. In the example presented below, the id column is a primary key for the table **“Users”**. 

**An example of a table (called Users) in a relational database:**



| Users |
|-------|
| user_id (Primary key) |
| name |
| email |


A column containing unique numeric values (integers) are generally used as a primary key because RDBMs systems can process them more quickly. 

**What is a foreign key?**  
A foreign key in one table refers to the primary key of another table and is used to link between the tables. 

**An example of a table called Orders, showing a foreign key:**


| Orders |
|--------|
| order_id (Primary Key) |
| user_id (Foreign Key) |


**Transactions in a relational database**  
Relational databases are associated with transactional databases, and it is an important concept to understand. A transaction in a database is a series of operations that are performed as if they are one single operation. Either all of them are saved in the database after they are performed, or none of them are saved. 

**An easy to understand example is a transaction in a banking system.** Let us say, A is transferring money to B. 

**Steps in which that happens:**  
1. A transfers $5 to B, so $5 is deducted from A's account.  
2. In the second step, $5 is added to B's account.  

This is where a transaction comes in: either results from both of these actions are saved, or none of them are saved. If a connection error or server outage would happen in between, the database would cancel the transaction: no action would be saved. 

The properties of transactions are represented by the acronym **ACID**. These properties are explained below: 

**Atomic:** All changes are performed as a single operation, and either all of the operations are performed and saved, or none of them are. 

**Consistency:** Database stays in a correct state before and after a transaction.

**Isolation:** Each transaction is isolated, so it acts like only one transaction is happening at a time. 

**Durability:** After a successful transaction, data persists, even in the event of a system failure. 


**What is a RDBMS (Relational Database Management System)?**  
RDBMS is the underlying software that manages relational databases, organizing data into rows and columns, and allowing users to perform CRUD (Create, Ready, Update, Delete) operations on it.  Examples: **MySQL, PostgreSQL, Oracle, etc.**


**What is SQL?**  
SQL (Structured Query Language) is the standard programming language used for interacting with RDBMSs. SQL queries allow an easy way to perform CRUD operations over a relational database. 

**Below is an intro to SQL, with simple examples:**  
**NOTE:** The following examples were run in `PostgreSQL`. 


**Create a table:**  
Define a table, and its structure

```sql
CREATE TABLE users (
user_id serial primary key,  
name varchar(80), 
email varchar(255) unique 
);
```

This creates a **“users”** table with three columns: **user_id**, **name**, and **email**.  
**serial:**  indicates primary key for users, and “serial” indicates autoincrementing four-byte integer  
**varchar(n):**  variable length char string of length n. 


**Remove a table:**  
 
```sql
DROP TABLE users;
```

**Populate a table with rows:**

```sql
INSERT INTO users(name, email)
VALUES (‘john’, ‘john22@gmail.com’);
```
The above statement adds a new row to the users table with name **‘john’**, and email **‘john22@gmail.com’**.


**Querying a table:**

```sql
SELECT * FROM users;
```
‘*’ is a shorthand for all columns.  

**Output:**  
```
 user_id | name |      email       
---------+------+------------------
       1 | John   |    john22@gmail.com
(1 row)
```

**NOTE:** `SELECT *` is considered bad in production code, because adding a column would affect/change the results. 


So if you wanted just the column **“name”**, you could do:  

```sql
SELECT name FROM users;
```

**Output:**  
```
name 
------
John
(1 row)
```

To get more than one column, use comma:  
```sql
SELECT name, email FROM users;
```

**Output:**  
```
name  |      email       
------+------------------
 John | john22@gmail.com
(1 row)
```


To introduce new SQL keywords – **AS**, **WHERE**, and **ORDER BY** – I have prepared a new table with new data. 

```
      name      | price  | discount 
----------------+--------+----------
 Iphone 8       | 500.00 |    10.00
 Ipad           | 300.00 |    20.00
 Acer monitor   | 200.00 |    33.00
 Magni Keyboard | 200.00 |    33.00
(4 rows)
```

**AS Keyword:**  
For the table above, you can write an expression that applies a discount to the current price, and then returns the product and the new discounted price as **“discount price”**, using the **“AS”** keyword. 

```sql
SELECT name, ROUND(price - ((discount / 100) * price)) AS discount_price 
FROM products;
```

**Output:**
```
      name      | discount_price 
----------------+----------------
 Iphone 8       |            450
 Ipad           |            240
 Acer monitor   |            134
 Magni Keyboard |            134
(4 rows)
```


**WHERE:**  
WHERE can be used to retrieve only those rows that satisfy your mentioned conditions.  
**Example:**  

```sql
SELECT name, price 
FROM products 
WHERE price <= 200;
```
**Output:**
```
      name      | price  
----------------+--------
 Acer monitor   | 200.00
 Magni Keyboard | 200.00
(2 rows)
```


**ORDER BY:**  
You can use ORDER BY to retrieve your results in a sorted order.  
Example shows retrieving the products sorted according to the price. 

```sql
SELECT name, price 
FROM products 
ORDER BY price;
```
**Output:**
```
      name      | price  
----------------+--------
 Acer monitor   | 200.00
 Magni Keyboard | 200.00
 Ipad           | 300.00
 Iphone 8       | 500.00
(4 rows)
```

**JOIN:**  
JOIN allows you to combine rows from two or more tables, based on a column related between them.  
**Example:**  

**Users table**  
```
 user_id | name |      email       
---------+------+------------------
       1 | John | john22@gmail.com
(1 row)
```

**Orders table**
```
 order_id | customer_id | order_date 
----------+-------------+------------
        1 |           1 | 2025-12-27
(1 row)
```

**Join operation:**  
```sql
SELECT u.user_id, u.name, u.email, o.order_id, o.order_date 
FROM users u 
JOIN orders o 
ON u.user_id = o.customer_id;
```


In the above example we have introduced `aliases` by relabeling **u** for users and **o** for orders.

**Output:**  
```
 user_id | name |      email       | order_id | order_date 
---------+------+------------------+----------+------------
       1 | John | john22@gmail.com |        1 | 2025-12-27
(1 row)
```

Join by default is **INNER JOIN**. Here, INNER JOIN returns only those users that actually have an order.

If we wanted all the users – even those users who did not have an order – we would use **LEFT JOIN**.  
**Example query and output:**  

```sql
select * from users u 
LEFT JOIN orders o 
ON u.user_id = o.customer_id;
```

**Output:**
```
 user_id | name |      email       | order_id | customer_id | order_date 
---------+------+------------------+----------+-------------+------------
       1 | John | john22@gmail.com |        1 |           1 | 2025-12-27
       2 | Ren  | ren212@gmail.com |          |             | 
(2 rows)
```

**Aggregate functions:**  
An **aggregate function** calculates a single result from multiple input rows. Examples of aggregate functions, and their use cases are mentioned below:  

**COUNT():** Count returns the number of rows.  

`COUNT(*)` -> Returns total number of rows for that table, including rows that have null values.  

**Example query:**  
```sql
SELECT COUNT(*) FROM products;
```

**Output:**  
```
 count 
-------
     4
(1 row)
```

`COUNT(column_name)` -> Returns total number of rows, excluding rows where that column is null.  

**Example query:**  
```sql
SELECT COUNT(price) FROM products;
```

**SUM():** Returns the total sum of a column from the selected table.  

**Example query:**  
```sql
SELECT SUM(price) FROM products;
```

**Output:**  
```
   sum   
---------
 1200.00
(1 row)
```

**AVG():** Returns the average value of a column from the selected table.  

**Example query:**  
```sql
SELECT ROUND(AVG(price)) FROM products;
```

**Output:**  
```
 round 
-------
   300
(1 row)
```

**MIN():** Returns the smallest value of a column from the selected table.  

**Example query:**  
```sql
SELECT MIN(price) FROM products;
```

**Output:**  
```
  min   
--------
 200.00
(1 row)
```

**MAX():** Returns the largest value of a column from the selected table.  

**Example query:**  
```sql
SELECT MAX(price) FROM products;
```

**Output:**  
```
  max   
--------
 500.00
(1 row)
```
**Sub-queries:**

The above queries that utilised aggregate functions MAX and MIN only provided one value from the column that we asked. What if I want all the columns or specific columns from that specific row with the smallest value or the max value? This is where sub-queries come in to help. 

**Example:**  
I want name, price, and discount columns for rows that have the smallest price. I can do a query like this:

```sql
SELECT name, price, discount 
FROM products 
WHERE price = (SELECT MIN(price) FROM products); -- This is a sub query.
```

**Output:**
```
      name      | price  | discount 
----------------+--------+----------
 Acer monitor   | 200.00 |    33.00
 Magni Keyboard | 200.00 |    33.00
(2 rows)
```

The query at the end, within the brackets, is our sub-query. 

Similarly, if we wanted the row with the max price, we could do something like this:  
```sql
SELECT name, price, discount 
FROM products 
WHERE price = (SELECT MAX(price) FROM products);
```

**Output:**  
```
   name   | price  | discount 
----------+--------+----------
 Iphone 8 | 500.00 |    10.00
(1 row)
```


**GROUP BY:**

For tables with multiple rows that have some common column values, such as the name of the student in the scores table, **GROUP BY** groups those rows first, and then applies your aggregate function. **GROUP BY** is often used with aggregate functions.  

**Example of a scores table:**
```
 name  | score |      course      
-------+-------+------------------
 Ren   |    92 | Chemistry
 Nancy |    85 | Physics
 Nancy |    91 | Biology
 Ren   |    84 | Biology
 Linus |    95 | Computer Science
 Linus |    99 | Mathematics
(6 rows)
```

Let us say, we want the average score of each student. We could do something like this:  
```sql
SELECT name, ROUND(AVG(score)) 
FROM scores 
GROUP BY name;
```

**Output:**  
```
 name  | avg 
-------+-----
 Nancy |  88
 Linus |  97
 Ren   |  88
(3 rows)
```

Here, **GROUP BY** first groups the students according to the name, and then the aggregate function is applied.  

**NOTE:** First **GROUP BY** is executed, which groups the rows according to the same values of the specified column, and then the aggregate function is executed on top of those grouped rows.  

**HAVING:**

**Having** can be used to filter rows that are grouped and aggregated. In other words, in a SQL query, “**HAVING**” is executed only after **GROUP BY** and an aggregation function is executed. 

For example, we can use the SQL query we wrote above, to return only those rows where the average score is greater than 90:  

```sql
SELECT name, ROUND(AVG(score)) AS avg 
FROM scores 
GROUP BY name 
HAVING ROUND(AVG(score)) >= 90;
```

**Output:**  
```
 name  | avg 
-------+-----
 Linus |  97
(1 row)
```


**FILTER:**  

**Filter** is a pre-aggregation method, meaning, it runs before aggregation is executed. It runs after grouping is executed, and allows you to apply aggregation only on the certain rows, according to your specifications.  

While writing a query, **FILTER** expects to be followed by an aggregation function.  

Let us say, we want the total number of courses where each student has achieved greater than 95, we could do something like this:  

```sql
SELECT name, COUNT(*) 
FILTER (WHERE score > 95) 
FROM scores 
GROUP BY name;
```

**Output:**  
```
 name  | count 
-------+-------
 Nancy |     0
 Linus |     1
 Ren   |     0
(3 rows)
```

Here, grouping is done first, and then the aggregation function `COUNT(*)` is applied only on those rows where the filter condition matches.  

**UPDATES:**

**UPDATE** allows you to update existing rows in a table.  

Let us apply this to our ‘scores’ table again.  

**Original scores table:**  
```
 name  | score |      course      
-------+-------+------------------
 Ren   |    92 | Chemistry
 Nancy |    85 | Physics
 Nancy |    91 | Biology
 Ren   |    84 | Biology
 Linus |    95 | Computer Science
 Linus |    99 | Mathematics
(6 rows)
```

For our scores table, let us say, we want to update the name of “Ren” to “Renu”.  

```sql
UPDATE scores 
SET name = 'Renu' 
WHERE name = 'Ren';
```

**Updated scores table:**  
```
 name  | score |      course      
-------+-------+------------------
 Nancy |    85 | Physics
 Nancy |    91 | Biology
 Linus |    95 | Computer Science
 Linus |    99 | Mathematics
 Renu  |    92 | Chemistry
 Renu  |    84 | Biology
(6 rows)
```

---

**Delete:**  

**Delete** allows you to delete existing rows from a table.  

**NOTE:** Delete commands do not ask for confirmation, so you must be careful when executing them.  

**Example:** To delete all the rows of “Nancy”, we could do something like this:  

```sql
DELETE FROM scores 
WHERE name = 'Nancy';
```

**Transactions:**

Like explained on the top of this post, a **transaction** in a database is a series of operations that are performed as if they are one single operation. Either all of them are saved in the database after they are performed, or none of them are saved.  

In PostgreSQL, `BEGIN` and `COMMIT` commands are used to set up a transaction.  
All the SQL commands of a transaction are surrounded by `BEGIN` and `COMMIT` commands.  

To proceed, an example of an accounts table has been created.  

**Original accounts table:**  
```
 name | balance 
------+---------
 A    |  100.00
 B    |  100.00
(2 rows)
```

Here, we will:  
- Deduct 100 from A’s account balance.  
- Add 100 to B’s account balance.  

**Example query:**  
```sql
BEGIN;                 

UPDATE accounts SET balance = balance - 100.00 
WHERE name = 'A' AND balance > 100;

UPDATE accounts SET balance = balance + 100.00 
WHERE name = 'B';

COMMIT;
```


If the server had crashed after the first update, then on restart, the database would roll back the transaction.  

In the above query, if account ‘A’ did not have enough balance, the first query would not update any rows. However, the second query would still run and credit B’s account with 100. Despite no rows being updated from the first query, it would still be regarded as a valid transaction. You can check for errors or perform validation during the transaction, and can abort the transaction using **ROLLBACK**.  

**Rollbacks** are generally explicitly initiated from the backend if a validation or a conditional check fails during mid-transaction.  

Below is an over-simplified example where the transaction is set to roll back if any validation fails or if an error occurs. The example below is written in **Go**, for the `accounts` table that was shown in the previous example.  

**Go Code Example:**

```go
func transferMoney() {
	tx, err := db.Begin()
	if err != nil {
		log.Printf("Error: %v \n", err)
		return
	}
	defer tx.Rollback() // defer a roll back in case anything fails.

	var balance float64 // Get the current balance of account A.
	if err := tx.QueryRow("SELECT balance FROM accounts WHERE name = 'A'").Scan(&balance); err != nil {
		if err == sql.ErrNoRows { // Confirm that the account exists.
			log.Printf("Account 'A' does not exist.")
			return
		}
	}

	if balance < 100 {
		log.Printf("Account 'A' has insufficient balance.")
		return
	}

	// Debit 100 from account A.
	_, err = tx.Exec("UPDATE accounts SET balance = balance - 100 WHERE name = 'A'")
	if err != nil {
		log.Printf("Error: %v \n", err)
		return
	}

	// Credit 100 from account B.
	_, err = tx.Exec("UPDATE accounts SET balance = balance + 100 WHERE name = 'B'")
	if err != nil {
		log.Printf("Error: %v \n", err)
		return
	}

	// Commit the transaction.
	if err := tx.Commit(); err != nil {
		log.Printf("Error: %v \n", err)
		return
	}
}
```

---

**Indexes:**

**Indexes** allow you to retrieve data more quickly. Think of it as an index in a book.  

Indexes can be made using the `CREATE INDEX` statement.

**Example** of creating an index on `id` for the products table:  
```sql
CREATE INDEX product_id ON products(id);
```

**NOTE:** For a table with a small number of rows, the database decides to scan the whole table instead of using an index.  

**NOTE:** Although indexes make reading/querying faster, updating/writing processes on that table will take longer. This is because indexes will also need an update.  


**How do you see if the index is being used?**  
- Use the **EXPLAIN** keyword  

```sql
EXPLAIN SELECT * FROM products WHERE id = 5;
```

You will get an output, similar to this:

```
                                 QUERY PLAN                                 
----------------------------------------------------------------------------
 Index Scan using product_id on products  (cost=0.29..8.31 rows=1 width=19)
   Index Cond: (id = 5)
(2 rows)
```


