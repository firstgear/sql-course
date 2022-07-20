* Course: SQL Mastery by Mosh
* Content: [course access](https://codewithmosh.com/courses/enrolled/525068)
* Instructor: Mosh Hamedami

# Getting Started (00:25)
## 1. Introduction (0:18)

## 2. What is SQL (3:24)

Structured Query Language

DB is collection of data stored in format that can easily be access. DBMS executes our instructions. 2 categories:

- relational: each table linked to other table, SQL language
- non-relational or NoSQL: no relationships

Not case sensitive, but use uppercase for SQL keywords (SELECT, AS, WHERE, FROM, ORDER BY).

## 3. Installing MySQL on Mac (4:58)

- Download MySQL Coummunity edition from mysql.com
- Download MySQL Workbench from mysql.com

## 4. Installing MySQL on Windows (5:20)
## 5. Creating the Databases (8:32)
## 6. What You'll Learn (2:31)

- Updating data
- Deleting data
- Summarizing data
- Writing Complex Queries with subqueries
- Built-in functions
- Triggers
- Events
- Transactions
- Concurrency
- Designing Databases
- Indexing for high performance
- Securing Databases

# Retrieving Data From a Single Table (00:53)
## 1. The SELECT Statement (5:50)

``` sql
USE sql_store; #DB
SELECT * FROM customers;
-- WHERE customer_id = 1
-- ORDER BY first_name;
```
Run query with SHIFT + COMMAND + ENTER

## 2. The SELECT Clause (8:48)

``` sql
SELECT 
	first_name, 
    last_name, 
    points, 
    points * 10 + 100 AS 'discount factor' -- arithmetic order of operations / alias
FROM customers;

SELECT 
	name,
    unit_price,
    unit_price * 1.1 AS 'new_price'
FROM products;
```

## 3. The WHERE Clause (5:17)

Comparison operator in SQL
```sql
SELECT *
FROM customers
WHERE points > 3000;

SELECT * 
FROM customers 
WHERE state != 'VA'; -- not located in Virginia

SELECT *
FROM customers
WHERE birth_date > '1990-01-01'; -- born after Jan 1 1990

-- Get the order placed this year
SELECT * FROM orders WHERE order_date >= "2019-01-01";

```

## 4. The AND, OR and NOT Operators (6:52)

``` sql
-- order logical operators AND, OR

SELECT *
FROM customers
WHERE birth_date > '1990-01-01' OR (points > 1000 AND state = 'VA');

-- from the order_items table, get the items for order #6
-- where the total price is greater than 30
SELECT * 
FROM order_items 
WHERE order_id = 6 AND 
(quantity * unit_price) > 30;
```

## 5. The IN Operator (3:03)

Use IN operator when you want to compare attribute with list of values.
``` sql
SELECT * 
FROM customers
WHERE state IN ('VA', 'FL', 'GA');

SELECT * 
FROM products 
WHERE quantity_in_stock IN (38, 49, 72);
```

## 6. The BETWEEN Operator (2:12)

```sql
SELECT * 
FROM customers
WHERE points BETWEEN 1000 AND 3000;

-- return customers born between ...
SELECT * 
FROM customers
WHERE birth_date BETWEEN '1990-1-1' AND '2000-1-1';
```

## 7. The LIKE Operator (5:37)

``` sql
-- lastname starts with b
SELECT * 
FROM customers
WHERE last_name LIKE 'b%';

-- lastname with b somewhere in last name
SELECT * 
FROM customers
WHERE last_name LIKE '%b%';

-- lastname ends with y
SELECT * 
FROM customers
WHERE last_name LIKE '%y';

-- lastname 6 characters, last one 'y'
SELECT * 
FROM customers
WHERE last_name LIKE '_____y';

-- get the customers whose addresses contain TRAIL or AVENUE
SELECT * 
FROM customers
WHERE address LIKE '%avenue%' OR address LIKE '%trail%';

-- get the customers whose phone number end with 9
SELECT * 
FROM customers
WHERE phone LIKE '%9';
```

## 8. The REGEXP Operator (9:21)

``` sql
SELECT *
FROM customers
WHERE last_name REGEXP 'field|mac|rose';
-- WHERE last_name LIKE '%field%'
-- ^ beginning
-- $ end of string
-- | logical or
-- [abc] any listed characters
-- [a-c] define range

-- get customers whose
--	first name are ELKA or AMBUR
SELECT * FROM customers WHERE first_name IN ('ambur','elka');
--  last name end with EY or ON
SELECT * FROM customers WHERE last_name REGEXP 'EY$|ON$';
--  last name start with MY or contains SE
SELECT * FROM customers WHERE last_name REGEXP '^MY|SE';
--  last names contain B followed by R or U
SELECT * FROM customers WHERE last_name REGEXP 'BR|BU';

```

## 9. The IS NULL Operator (2:26)

``` sql
SELECT *
FROM customers
WHERE phone IS NULL;

SELECT *
FROM customers
WHERE phone IS NOT NULL; -- inverse

-- get the orders that are not shipped yet
SELECT *
FROM orders
WHERE shipped_date IS NOT NULL;


```

## 10. The ORDER BY Clause (7:06)

``` sql
SELECT first_name, last_name FROM customers
ORDER BY birth_date;

SELECT first_name, last_name FROM customers
ORDER BY birth_date DESC;

SELECT first_name, last_name, 10 AS points
FROM customers
ORDER BY birth_date, first_name;

SELECT *, quantity * unit_price AS total_price
FROM order_items
WHERE order_id = '2'
ORDER BY total_price DESC;
```

## 11. The LIMIT Clause (3:26)

``` sql
SELECT *
FROM customers
LIMIT 6, 3
-- page 1: 1-3
-- page 2: 4-6
-- page 3: 7-9

-- get the top 3 loyal customers
SELECT *
FROM customers
ORDER BY points DESC
LIMIT 3
```

# Retrieving Data From Multiple Tables (01:02)

## 1. Inner Joins (8:26)

- INNER
- OUTER
    - LEFT
    - RIGHT

``` sql
SELECT order_id, o.customer_id, first_name, last_name
FROM orders o
INNER JOIN customers c
	ON o.customer_id = c.customer_id;

SELECT order_id, p.product_id, quantity, oi.unit_price
FROM order_items oi
JOIN products p
	ON p.product_id = oi.product_id;
```

## 2. Joining Across Databases (2:47)

## 3. Self Joins (4:13)

Join table with itself.
``` sql
USE sql_hr;
SELECT
	e.employee_id,
    e.first_name,
    m.first_name AS manager
FROM employees e
JOIN employees m
	ON e.employee_id = m.reports_to
```

## 4. Joining Multiple Tables (6:46)

``` sql
USE sql_store;
SELECT
	orders.order_id,
    orders.order_date,
    customers.first_name,
    customers.last_name,
    order_statuses.name AS status
FROM orders
JOIN customers
	ON customers.customer_id = orders.customer_id
JOIN order_statuses
	ON order_statuses.order_status_id = orders.status;

USE sql_invoicing;
SELECT
	payments.date,
    payments.invoice_id,
	payments.client_id,
    clients.name,
    payment_methods.name
FROM payments
JOIN payment_methods
	ON payments.payment_method = payment_methods.payment_method_id
JOIN clients
	ON payments.client_id = clients.client_id;
```

## 5. Compound Join Conditions (3:41)

Composite primary key, so you cannot identify item based on single column. Combine values in multiple columns to uniquely identify item.

``` sql
USE sql_store;
SELECT * 
FROM order_items
JOIN order_item_notes
	ON order_items.order_id = order_item_notes.order_id
    AND order_items.product_id = order_item_notes.product_id;
```
0 items returned

## 6. Implicit Join Syntax (2:20)

Be aware of other way to write JOIN query.
``` sql
USE sql_store;
SELECT * 
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id;

-- ! if you forget WHERE clause, you get cross join !
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id;
```

## 7. Outer Joins (6:27)

2 types of JOINS, inner and outer joins. INNER keyword is optional, so INNER JOIN by default.

INNER join see only customers with order due to JOIN condition.
``` sql
SELECT
	customers.customer_id,
    customers.first_name,
    orders.order_id
FROM customers
JOIN orders
	ON customers.customer_id = orders.customer_id
ORDER BY customers.customer_id;
```

OUTER join see all customers whether they have order or not, so order_id column becomes NULL, two types:
- LEFT JOIN (all records left table are returned)
- RIGHT JOIN (all records right table are returned), in this case identical to INNER join.

``` sql
SELECT
	customers.customer_id,
    customers.first_name,
    orders.order_id
FROM customers
LEFT JOIN orders
	ON customers.customer_id = orders.customer_id
ORDER BY customers.customer_id;
```

Exercise
```sql
SELECT
	products.product_id,
    products.name,
    order_items.quantity
FROM products
LEFT JOIN order_items
	ON order_items.product_id = products.product_id
ORDER BY product_id;
```

## 8. Outer Join Between Multiple Tables (6:18)

As a best practice don't use RIGHT JOIN clause as it makes code difficult to understand, use preferably LEFT JOIN as OUTER JOIN clause.

``` sql
SELECT
	c.customer_id,
    c.first_name,
    o.order_id,
    sh.name AS shipper
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.customer_id
LEFT JOIN shippers sh
	 ON o.shipper_id = sh.shipper_id
ORDER BY c.customer_id;

SELECT
    o.order_id,
    o.order_date,
    c.first_name AS customer
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
JOIN shippers sh
    ON o.shipper_id = sh.shipper_id;

--- exercise
SELECT
	orders.order_date,
	orders.order_id,
	customers.first_name AS customer,
	shippers.name AS shipper,
	order_statuses.name AS status
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id
JOIN order_statuses ON orders.status = order_statuses.order_status_id
LEFT JOIN shippers ON orders.shipper_id = shippers.shipper_id;
```

## 9. Self Outer Joins (2:12)

``` sql
USE sql_hr;
SELECT
	e.employee_id,
	e.first_name,
	m.first_name AS manager
FROM employees e
JOIN employees m ON e.reports_to = m.employee_id; -- will only return people with manager
LEFT JOIN employees m ON e.reports_to = m.employee_id; -- will also return CEO
```

## 10. The USING Clause (5:22)

If the column name is exactly the same accross 2 tables. USING clause is shorter and easier to read.

``` sql
USE sql_store;
SELECT
	o.order_id,
	c.first_name
FROM orders o
JOIN customers c 
	-- ON o.customer_id = c.customer_id;
    USING (customer_id);

SELECT *
FROM order_items oi
JOIN order_item_notes oin
USING (order_id, product_id);

-- exercise
USE sql_invoicing;
SELECT
	p.date,
	p.amount,
	c.name AS client,
	pm.name
FROM payments p
JOIN clients c USING (client_id)
JOIN payment_methods pm ON pm.payment_method_id = p.payment_method;
```

## 11. Natural Joins (1:21)

Will join multiple tables on common columns without specifying those columns. Easy to code but can be dangerous because DB engine choses which columns to JOIN.

``` sql
SELECT
    o.order_id,
    c.first_name
FROM orders o
NATURAL JOIN customers c;
```

## 12. Cross Joins (3:14)

Combine every column from table 1 with every column form table 2. Usecase tables of size (S,M,L) with table of colours and want to combine all options.

``` sql
SELECT
	c.first_name AS customer,
	p.name AS product
FROM customers c
CROSS JOIN products p
ORDER BY c.first_name;

-- exercise explicit notation
SELECT
	sh.name as shipper,
	p.name AS product
FROM shippers sh
CROSS JOIN products p
ORDER BY sh.name;

-- exercise implicit notation
SELECT
	sh.name as shipper,
	p.name AS product
FROM shippers sh, products p
ORDER BY sh.name;
```

## 13. Unions (8:29)

Combine multiple rows from different queries, must have equal number of columns.

``` sql
SELECT
	order_id,
    order_date,
    'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'

UNION

SELECT
	order_id,
    order_date,
    'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01';

-- exercise
SELECT
	customer_id,
	first_name,
	points,
	'Bronze' AS type
FROM customers
WHERE 2000 > points

UNION

SELECT
	customer_id,
	first_name,
	points,
	'Silver' AS type
FROM customers
WHERE 2000 <= points AND points <= 3000

UNION

SELECT
	customer_id,
	first_name,
	points,
	'Gold' AS type
FROM customers
WHERE 3000 < points

ORDER BY first_name;
```

- A Quick Note

# Inserting, Updating, and Deleting Data (00:42)
## 1. Column Attributes (3:24)

Attributes:
- Column name
- [Datatypes](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)
  - INT: whole numbers, no decimal points
  - VARCHAR: Variable Character, store string or textual values, can be slower, but does not waste storage.
  - CHAR: eg. CHAR(15) will always consume 15 bytes, fastest to store and retrieve but can waste storage space.
  - DATE
- PK: Primary Key (true/false)
- NN: Not null
- AI: Auto Increment
- Default value for column

## 2. Inserting a Row (5:46)

``` sql
INSERT INTO customers VALUE (
	DEFAULT,
    'John',
    'Smith', 
    '1990-01-01',
    NULL,
    'address',
    'city',
    'CA',
    200);

INSERT INTO customers (first_name, last_name, birth_date, address, city, state)
VALUES (
    'John',
    'Smith', 
    '1990-01-01',
    'address',
    'city',
    'CA');
```

## 3. Inserting Multiple Rows (3:18)

``` sql
INSERT INTO shippers (name)
VALUES  ('Shipper1'),
		('Shipper2'),
        ('Shipper3');

INSERT INTO products (name, quantity_in_stock, unit_price)
VALUES  ('Organic White Rose Tea', 25, 0.25),
		('Green tea', 30, 0.20),
		('Berentee', 20, 0.35),
        ('Pickwick', 100, 0.10);
```

## 4. Inserting Hierarchical Rows (5:53)

Insert data in multiple tables. Orders table can have one or more children in the order_items table.

```sql
INSERT INTO orders (customer_id, order_date, status)
VALUES (1, '2019-01-02', 1);

INSERT INTO order_items
VALUES 
	(LAST_INSERT_ID(), 1, 1, 2.95),
	(LAST_INSERT_ID(), 2, 1, 3.95);
	
SELECT LAST_INSERT_ID(); -- returns Auto Increment ID
```

## 5. Creating a Copy of a Table (8:47)

Create copy of table called table_archive. Mysql will ignore attributes such as PK, AI.

```sql
CREATE TABLE orders_archive AS
SELECT * FROM orders; -- subquery, part of other SQL statement

INSERT INTO orders_archive
SELECT *
FROM orders
WHERE order_date < '2019-01-01';

-- exercise JOIN becomes new table
CREATE TABLE invoices_new AS
SELECT 
	invoices.invoice_id,
	invoices.number,
    clients.name AS client_name,
    invoices.invoice_total,
    invoices.payment_total,
    invoices.invoice_date,
    invoices.due_date,
    invoices.payment_date
FROM invoices
JOIN clients
	USING (client_id)
WHERE invoices.payment_date IS NOT NULL;
```

## 6. Updating a Single Row (3:55)

```sql
UPDATE invoices
SET payment_total = default, payment_date = NULL
WHERE invoice_id = 1;

UPDATE invoices
SET 
	payment_total = payment_total * 0.5,
	payment_date = due_date
WHERE invoice_id = 3;
```

## 7. Updating Multiple Rows (3:14)

Similar to updating single row but more general condition.

```sql
UPDATE invoices
SET 
	payment_total = payment_total * 0.5,
	payment_date = due_date
WHERE invoice_id = 3;

-- exercise
UPDATE customers
SET 
	points = points + 50
WHERE birth_date < '1990-01-01';
```

## 8. Using Subqueries in Updates (5:36)

A subquery is a SELECT statement that is within another SQL statement.

```sql
USE sql_invoicing;

UPDATE invoices
SET 
	payment_total = payment_total * 0.5,
	payment_date = due_date
-- for single record returned use =
WHERE client_id =
(SELECT client_id
FROM clients
WHERE name = 'MyWorks');

-- for multiple records returned in subquery use IN
WHERE client_id IN
(SELECT client_id
FROM clients
WHERE state IN ('CA', 'NY'));

-- first run SELECT statement to make sure running UPDATE against correct records
SELECT *
FROM invoices
WHERE payment_date IS NULL;

-- then add UPDATE
UPDATE invoices
SET 
	payment_total = payment_total * 0.5,
	payment_date = due_date
WHERE payment_date IS NULL;

-- exercise "gold customer" > 3000 points
USE sql_store;
SELECT * FROM orders
WHERE comments IS NULL;

USE sql_store;
UPDATE orders
SET comments = "gold customer"
WHERE customer_id IN
(SELECT customer_id FROM customers
WHERE points > 3000);
```

## 9. Deleting Rows (1:24)

```sql
USE sql_invoicing;

DELETE FROM invoice; -- deletes all records from table

DELETE FROM invoice
WHERE invoice_id = 1; -- delete single record

DELETE FROM invoice 
WHERE client_id = 
(SELECT *
FROM clients
WHERE name = "Myworks"); -- with subquery
```

## 10. Restoring the Databases (1:06)

Restore DB to original state, we have added/deleted/updated data.

# Summarizing Data (00:33)

## 1. Aggregate Functions (9:19)

Relevant for organizations with lots of data, take series of values and aggregate them to produce single value. They don't operate on NULL values. Attribute is either column_name or expression. By default these functions use duplicate values, if you don't use DISTINCT keyword.

- MAX(column_name)
- MIN(...)
- AVG()
- SUM()
- COUNT()

```sql
SELECT 
	MAX(invoice_total) AS highest,
	MIN(invoice_total) AS lowest,
    AVG(invoice_total) AS average,
    SUM(invoice_total) AS total,
    COUNT(invoice_total) AS number_of_invoices,
    COUNT(payment_date) AS count_of_payments,
    COUNT(*) AS total_records,
	COUNT(DISTINCT client_id) AS total_distinct_records
FROM invoices
WHERE invoice_date > '2019-07-01'; -- filter on aggregate

-- exercise
(SELECT 
	"First half of 2019" AS date_range,
	SUM(invoice_total) AS total_sales,
	SUM(payment_total) AS total_payments,
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN '2019-01-01' AND '2019-07-01')
UNION
(SELECT 
	"Second half of 2019" AS date_range,
	SUM(invoice_total) AS total_sales,
	SUM(payment_total) AS total_payments,
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN '2019-07-01' AND '2019-12-31')
UNION
(SELECT
	"Total" AS date_range,
	SUM(invoice_total) AS total_sales,
	SUM(payment_total) AS total_payments,
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date
	BETWEEN '2019-01-01' AND '2019-12-31');
```

## 2. The GROUP BY Clause (7:21)

What if we want to see the total sales per client?

```sql
SELECT
	SUM(invoice_total) AS total_sales
FROM invoices; -- TOTAL

SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
WHERE invoice_date >= '2019-07-01'
GROUP BY client_id
ORDER BY total_sales DESC;

SELECT
	state, city,
	SUM(invoice_total) AS total_sales
FROM invoices i
JOIN clients USING (client_id)
GROUP BY state, city; -- grouping by multiple columns

-- exercise
SELECT
	date,
    pm.name AS payment_method,
    SUM(amount) AS total_payments
FROM payments p
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
GROUP BY date, payment_method
ORDER BY date;
```

Close TAB = Command + W

## 3. The HAVING Clause (8:50)

```sql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
WHERE total_sales > 500
GROUP BY client_id; --- ERROR unknown column total sales

SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
GROUP BY client_id
HAVING total_sales > 500; -- WORKING, filter data after grouping.

SELECT
	client_id,
	SUM(invoice_total) AS total_sales,
    COUNT(*) AS number_of_invoices
FROM invoices
GROUP BY client_id
HAVING total_sales > 500 AND number_of_invoices > 5; -- compound search condition

-- exercise
-- Get the customers
-- 	located in Virginia
--	who have spent more than $100
SELECT
	customers.customer_id,
	customers.first_name,
	customers.last_name,
	customers.state,
    SUM(order_items.quantity * 	order_items.unit_price) AS total_sales
FROM customers
JOIN orders USING (customer_id)
JOIN order_items USING (order_id)
WHERE state = 'VA'
GROUP BY customers.customer_id;
HAVING total_sales > 100;
```

Use:
- WHERE: filter data before grouping our rows, can be any columns.
- HAVING: filter data after grouping our rows, columns have to be mentioned in SELECT clause

## 4. The ROLLUP Operator (5:05)

Operator that applies to aggregate values, only available in MySQL.

```sql
SELECT
	state,
    city,
    SUM(invoice_total) AS total_sales
FROM invoices
JOIN clients USING (client_id)
GROUP BY state, city WITH ROLLUP;

-- exercise
SELECT
	pm.name AS payment_method,
    SUM(p.amount) AS total
FROM payments p
JOIN payment_methods pm
	ON p.payment_method = pm.payment_method_id
GROUP BY pm.name WITH ROLLUP;
```

# Writing Complex Query (00:45)

## 1. Introduction (1:28)

Revisit subquery, SELECT statement within another SQL statement.

## 2. Subqueries (2:29)

```sql
-- Find products that are more
-- expensive than Lettuce ( id = 3 )

SELECT *
FROM products
WHERE unit_price > (
	SELECT unit_price
    FROM products
    WHERE product_id = 3
    );

-- In sql_hr;
-- 		Find employees whose earn more than average
USE sql_hr;

SELECT *
FROM employees
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    );
```

## 3. The IN Operator (3:39)

```sql
-- Find the products that have never been ordered.
USE sql_store;
SELECT *
FROM products
WHERE product_id NOT IN (
	SELECT DISTINCT product_id
    FROM order_items
    );

-- Find clients without invoices
USE sql_invoicing;
SELECT *
FROM clients
WHERE client_id NOT IN (
	SELECT DISTINCT client_id
    FROM invoices
    );
```

## 4. Subqueries vs Joins (5:07)

INNER join will give all clients with invoices.
OUTER join or LEFT join will give clients without invoices.

Trade-off
- readability
- performance

Identical result as above.
```sql
-- Find clients without invoices
USE sql_invoicing;
SELECT *
FROM clients
LEFT JOIN invoices USING (client_id)
WHERE invoice_id IS NULL;

-- Find customers who have ordered lettuce (id = 3)
-- 	Select customer_id, first_name, last_name

-- JOIN SOLUTION
USE sql_store;
SELECT DISTINCT
	customer_id,
    first_name,
    last_name
FROM customers
JOIN orders USING (customer_id)
JOIN order_items USING (order_id)
WHERE product_id = 3;

-- SUBQUERY SOLUTION
--- not very readable !
USE sql_store;
SELECT 
	customer_id,
    first_name,
    last_name
FROM customers
WHERE customer_id IN (
	SELECT customer_id
    FROM orders
    WHERE order_id IN (
		SELECT order_id
        FROM order_items
        WHERE product_id = 3));
```

## 5. The ALL Keyword (4:52)

```sql
-- Select invoices larger than all invoice of client 3
USE sql_invoicing;
SELECT *
FROM invoices
WHERE invoice_total > (
	SELECT MAX(invoice_total)
    FROM invoices
    WHERE client_id = 3
	);

-- using ALL keyword
SELECT *
FROM invoices
WHERE invoice_total > ALL(
	SELECT invoice_total
    FROM invoices
    WHERE client_id = 3
	);
```

## 6. The ANY Keyword (2:36)

ANY/SOME.
= ANY equal to IN

```sql
-- Select clients with at least two invoices
SELECT *
FROM clients
WHERE client_id IN (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
);

SELECT *
FROM clients
WHERE client_id = ANY (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
);
```

## 7. Correlated Subqueries (5:36)

```sql
-- Select employees whose salary is above the average in their office.
USE sql_hr;

-- AVG salary of whole group, single average across whole table.
SELECT *
FROM employees
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    )
;

-- AVG salary per office
-- for each employee
-- 		calculate the avg salary for employee office
-- 		return the employee if salary > avg
-- reference alias from outer query, this subquery executed multiple times
USE sql_hr;
SELECT *
FROM employees e
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    WHERE office_id = e.office_id
    )
;

-- exercise
-- Get invoices that are larger than the 
-- client's average invoice amount
USE sql_invoicing;
SELECT *
FROM invoices i
WHERE invoice_total > (
	SELECT AVG(invoice_total)
    FROM invoices
    WHERE client_id = i.client_id
    )
;
```

## 8. The EXISTS Operator (5:39)



9. Subqueries in the SELECT Clause (4:29)
10. Subqueries in the FROM Clause (2:58)

# Essential MySQL Functions (00:33)
1. Numeric Functions (2:54)
2. String Functions (5:47)
3. Date Functions in MySQL (4:08)
4. Formatting Dates and Times (2:14)
5. Calculating Dates and Times (3:08)
6. The IFNULL and COALESCE Functions (3:29)
7. The IF Function (4:54)
8. The CASE Operator (5:23)

# Views (00:18)
1. Creating Views (5:36)
2. Altering or Dropping Views (2:52)
3. Updatable Views (5:12)
4. THE WITH OPTION CHECK Clause (2:18)
5. Other Benefits of Views (2:37)

# Stored Procedures (00:48)
1. What are Stored Procedures (2:18)
2. Creating a Stored Procedure (5:34)
3. Creating Procedures Using MySQLWorkbench (1:21)
4. Dropping Stored Procedures (2:09)
5. Parameters (5:26)
6. Parameters with Default Value (8:18)
7. Parameter Validation (6:40)
8. Output Parameters (3:55) 
9. Variables (4:33)
10. Functions (6:28)
11. Other Conventions (1:51)
- A Quick Note

# Triggers and Events (00:22)
 
1. Triggers (7:31)
2. Viewing Triggers (1:20)
3. Dropping Triggers (0:52)
4. Using Triggers for Auditing (4:52)
5. Events (4:33)
6. Viewing, Dropping and Altering Events (2:04)

# Transactions and Concurrency (00:49)
1. Transactions (2:44)
2. Creating Transactions (5:11)
3. Concurrency and Locking (4:07)
4. Concurrency Problems (7:25)
5. Transaction Isolation Levels (5:42)
6. READ UNCOMMITTED Isolation Level (3:26)
7. READ COMMITTED Isolation Level (3:01)
8. REPEATABLE READ Isolation Level (3:29)
9. SERIALIZABLE Isolation Level (2:18)
10. Deadlocks (6:11)

# Data Types (00:35)
1. Introduction (0:43)
2. String Types (2:25)
3. Integer Types (2:52)
4. Fixed-point and Floating-point Types (1:42)
5. Boolean Types (0:46)
6. Enum and Set Types (3:36)
7. Date and Time Types (0:44)
8. Blob Types (1:17)
9. JSON Type (10:24)

# Designing Databases (01:30)
1. Introduction (1:25)
2. Data Modelling (2:26)
3. Conceptual Models (4:34)
4. Logical Models (7:24)
5. Physical Models (6:27)
6. Primary Keys (3:23)
7. Foreign Keys (5:48)
8. Foreign Key Constraints (5:22)
9. Normalization (1:24)
10. 1NF- First Normal Form (2:42)
11. Link Tables (4:01)
12. 2NF- Second Normal Form (6:32)
13. 3NF- Third Normal Form (1:43)
14. My Pragmatic Advice (2:55)
15. Don't Model the Universe (4:24)
16. Forward Engineering a Model (2:35)
17. Synchronizing a Model with a Database (4:48)
18. Reverse Engineering a Database (3:11)
19. Project- Flight Booking System (0:23)
20. Solution- Conceptual Model (7:59)
21. Solution- Logical Model (9:03)
22. Project - Video Rental Application (1:05)
23. Solution- Conceptual Model (6:59)
24. Solution- Logical Model (8:29)
25. Creating and Dropping Databases (1:41)
26. Creating Tables (3:13)
27. Altering Tables (2:56)
28. Creating Relationships (4:47)
29. Altering Primary and Foreign Key Constraints (2:10)
30. Character Sets and Collations (6:29)
31. Storage Engines (2:27)
- A Quick Note

# Indexing for High Performance (00:58)
1. Introduction (0:41)
2. Indexes (2:49)
3. Creating Indexes (5:00)
4. Viewing Indexes (3:19)
5. Prefix Indexes (3:40)
6. Full-text Indexes (7:50)
7. Composite Indexes (5:12)
8. Order of Columns in Composite Indexes (9:16)
9. When Indexes are Ignored (5:03)
10. Using Indexes for Sorting (7:02)
11. Covering Indexese (1:58)
12. Index Maintenance (1:25)
13. Performance Best Practices

# Securing Databases (00:20)
1. Introduction (0:33)
2. Creating a User (3:12)
3. Viewing Users (1:29)
4. Dropping Users (0:48)
5. Changing Passwords (1:06)
6. Granting Privileges (4:53)
7. Viewing Privileges (1:34)
8. Revoking Privileges (1:20)
9. Wrap Up (0:44)