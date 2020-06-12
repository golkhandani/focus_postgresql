# POSTGRESQL COMMANDS

#### a. `DDL` (DATA DEFINITION LANGUAGE) COMMANDS
Commands that are used for defining database schema such as `CREATE`, `DROP`, `ALTER`, etc.

#### b. `DML` (DATA MANIPULATION LANGUAGE) COMMANDS
Commands that are used for data presentation such as `SELECT`, `INSERT`, `UPDATE`, `DELETE`, etc.


#### c. `DCL` (DATA CONTROL LANGUAGE) COMMANDS
Commands that are used for permission controls such as `GRANT`, `INVOKE`, etc.


#### d. `TCL` (TRANSACTION CONTROL LANGUAGE) COMMANDS
Commands that are used for controlling transactions   such as `COMMIT`, `ROLLBACK`, `SAVEPOINT`, etc.

## 0. Alias
### 0.1 Column alias
```sql
SELECT { column_name | expression } AS alias_name
FROM table;
```
for example: 
```sql
SELECT
    first_name || ' ' || last_name AS full_name
FROM
    customer
ORDER BY 
    full_name;
```
### 0.2 Table alias
```sql
SELECT
    column_list
FROM
    table_name AS alias_name;
```
The practical uses of the table alias are when you query data from multiple tables that have the same column names. In this case, you must qualify the columns using the table names as follows:

```sql
SELECT t1.column_name, 
     t2.column_name
FROM table_name1 t1
INNER JOIN table_name2 t2 ON join_predicate;
```
Second, when you join a table to itself a.k.a self-join, you must use table aliases. Because PostgreSQL does not allow you to reference the same table multiple times within a query.

```sql
SELECT
    colum_list
FROM
    table_name table_alias
INNER JOIN table_name ON join_predicate;
```

## 1. Querying Data
QUERY EVALUATION ORDER
- First, the product of all tables in the `FROM` clause is formed.
- The `WHERE` clause is then evaluated to eliminate rows that do not satisfy the `search_condition`.
- Next, the rows are grouped using the columns in the `GROUP BY` clause.
- Then, groups that do not satisfy the `search_condition` in the `HAVING` clause are eliminated.
- Next, the expressions in the `SELECT` statement target list are evaluated.
- If the `DISTINCT` keyword in present in the select clause, duplicate rows are now eliminated.
- The `UNION` is taken after each `sub-select` is evaluated.
- Finally, the resulting rows are sorted according to the columns specified in the `ORDER BY` clause.
- `TOP` clause is executed.
### 1.1 SELECT
```sql
SELECT
   select_list
FROM
   table_name;
```

##### The `SELECT` statement has the following clauses:
- Select distinct rows using `DISTINCT` operator.
- Sort rows using `ORDER BY` clause.
- Filter rows using `WHERE` clause.
- Select a subset of rows from a table using `LIMIT` or - `FETCH` clause.
- Group rows into groups using `GROUP BY` clause.
- Filter groups using `HAVING` clause.
- Join with other tables using joins such as `INNER JOIN`, - `LEFT JOIN`, `FULL OUTER JOIN`, `CROSS JOIN clauses`.
- Perform set operations using UNION, INTERSECT, and EXCEPT.
### 1.2 ORDER BY
```sql
SELECT
	column_1,
	column_2
FROM
	table_name
ORDER BY
	column_1 [ASC | DESC],
	column_2 [ASC | DESC];
```
### 1.3 DISTINCT and  DISTINCT ON 
The `DISTINCT` clause keeps one row for each group of duplicates.
```sql
SELECT
   DISTINCT column_1
FROM
   table_name;
```
> In this statement, the values in the column_1 column are used to evaluate the duplicate.

```sql
SELECT
   DISTINCT column_1, column_2
FROM
   table_name;
```
> In this case, the unique combination of values in both column_1 and column_2 columns will be used for evaluating the duplicate.


The `DISTINCT ON` (expression) to keep the “first” row of each group of duplicates using
```sql
SELECT
   DISTINCT ON (column_1) column_alias,
   column_2
FROM
   table_name
ORDER BY
   column_1,
   column_2;
```

> The order of rows returned from the SELECT statement is unpredictable therefore the “first” row of each group of the duplicate is also unpredictable. It is good practice to always use the ORDER BY clause with the DISTINCT ON(expression) to make the result set obvious.


## 2. Filtering Data

### 2.1 WHERE
```sql
SELECT select_list
FROM table_name
WHERE condition;
```
The following table illustrates the standard comparison operators.
|Operator|	Description | Example
|-|-|-|
|   =           |   Equal                   | WHERE first_name = 'Jamie';
|   >           |   Greater than            | 
|   <           |   Less than               |
|   >=          |   Greater than or equal   |
|   <=          |   Less than or equal      |
|   <> or !=    |   Not equal               | WHERE last_name <> 'Motley';
|   AND         |	Logical operator AND    | WHERE first_name = 'Jamie' AND last_name = 'Rice';
|   OR          |   Logical operator OR     | WHERE first_name = 'Jamie' OR last_name = 'Rice';
|   IN          |   In a list of data       | WHERE first_name IN ('Ann','Anne','Annie');
|   LIKE/ILIKE  |   Matches a pattern       | WHERE first_name LIKE 'Ann%';
|   BETWEEN     |   Is in a range of values | WHERE LENGTH(first_name) BETWEEN 3 AND 5;

### 2.2 LIMIT AND FETCH
> Use the LIMIT clause to get the number of highest or lowest items in a table
```sql
SELECT
	*
FROM
	table_name
LIMIT n;
```
In case you want to skip a number of rows before returning the n rows, you use OFFSET clause placed after the LIMIT clause as the following statement:
> If you use a large OFFSET, it might not be efficient because PostgreSQL still has to calculate the rows skipped by the OFFSET inside the database server, even though the skipped rows are not returned.
```sql
SELECT
	*
FROM
	table
LIMIT n OFFSET m;
```

```sql
OFFSET start_row_count { ROW | ROWS }
FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY
```


### 2.3 IN
```sql
value { NOT IN | IN } (value1,value2,...)
```
or
```sql
value { NOT IN | IN } (SELECT value FROM tbl_name);
```
> You can rewrite IN operator by using the equal (=) and OR operators;
> You can also rewrite the NOT IN operator by using the not equal (<>) and the AND operators

### 2.4 BETWEEN
```sql
value { BETWEEN | NOT BETWEEN } low AND high;
```
for numbers, dates, etc.
### 2.5 LIKE and ILIKE
```sql
string LIKE pattern
```
`ILIKE` means case insensitive `LIKE`

> You can combine the percent ( %) with underscore ( _) to construct

### 2.6 IS NULL
```sql
value { IS NULL | IS NOT NULL }
```

## 3. Joining Multiple Tables
PostgreSQL join is used to combine columns from one (self-join) or more tables based on the values of the common columns between the tables. The common columns are typically the primary key columns of the first table and foreign key columns of the second table.

PostgreSQL supports `inner join`, `left join`, `right join`, `full outer join`, `cross join`, `natural join`, and a special kind of join called `self-join`.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Joins.png)


### 3.1 INNER JOIN
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Inner-Join.png)

```sql
SELECT
	A.pka,
	A.c1,
	B.pkb,
	B.c2
FROM
	A
INNER JOIN B ON A .pka = B.fka;
```
- First, you specify the column in both tables from which you want to select data in the SELECT clause.
- Second, you specify the main table i.e.,  A in the FROM clause.
- Third, you specify the table that the main table joins to i.e., B in the INNER JOIN clause. In addition, you put a join condition after the ON keyword i.e, A.pka = B.fka.

>The primary key column ( pka) and foreign key column ( fka) are typically indexed; therefore, PostgreSQL only has to check for the match in the indexes, which is very fast.

### 3.2 LEFT JOIN and RIGHT JOIN
![left join](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join.png)
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join-with-Where.png)

Each row in the A table may have zero or many corresponding rows in the B table. Each row in the B table has one and only one corresponding row in the A table.

If you want to select rows from the A table that have corresponding rows in the B table, you use the INNER JOIN clause.

If you want to select rows from the A table which may or may not have corresponding rows in the B table, you use the LEFT JOIN clause. In case, there is no matching row in the B table, the values of the columns in the B table are substituted by the NULL values.
```sql
SELECT
	A.pka,
	A.c1,
	B.pkb,
	B.c2
FROM
	A
LEFT JOIN B ON A .pka = B.fka;
```
- Specify the columns from which you want to select data in the SELECT clause.
- specify the left table i.e., A table where you want to get all rows, in the FROM clause.
- Specify the right table i.e., B table in the LEFT JOIN clause. In addition, specify the condition for joining two tables.
>The LEFT JOIN clause returns all rows in the left table ( A) that are combined with rows in the right table ( B) even though there is no corresponding rows in the right table ( B).


The `right join` or `right outer join` is a reversed version of the left join. It produces a result set that contains all rows from the `right` table with matching rows from the `left` table. If there is no match, the left side will contain null values.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Right-Join.png)
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Right-Join-with-Where.png)

```sql
SELECT
    a.id id_a,
    a.fruit fruit_a,
    b.id id_b,
    b.fruit fruit_b
FROM
    basket_a a
RIGHT JOIN basket_b b ON a.fruit = b.fruit;
```

### 3.3 JOIN (SELF-JOIN)
A self-join is a query in which a table is joined to itself. Self-joins are useful for comparing values in a column of rows within the same table.

```sql
SELECT column_list
FROM A a1
INNER JOIN A b1 ON join_predicate;
```

#### Use Cases:
#### 1) Querying hierarchy data
```sql
CREATE TABLE employee (
	employee_id INT PRIMARY KEY,
	first_name VARCHAR (255) NOT NULL,
	last_name VARCHAR (255) NOT NULL,
	manager_id INT,
	FOREIGN KEY (manager_id) 
	REFERENCES employee (employee_id) 
	ON DELETE CASCADE
);
INSERT INTO employee (
	employee_id,
	first_name,
	last_name,
	manager_id
)
VALUES
	(1, 'Windy', 'Hays', NULL),
	(2, 'Ava', 'Christensen', 1),
	(3, 'Hassan', 'Conner', 1),
	(4, 'Anna', 'Reeves', 2),
	(5, 'Sau', 'Norman', 2),
	(6, 'Kelsie', 'Hays', 3),
	(7, 'Tory', 'Goff', 3),
	(8, 'Salley', 'Lester', 3);
```
```sql
SELECT
    e.first_name || ' ' || e.last_name employee,
    m .first_name || ' ' || m .last_name manager
FROM
    employee e
INNER JOIN employee m ON m .employee_id = e.manager_id
ORDER BY
    manager;
```
> To include the top manager in the result set, you use the `LEFT JOIN` instead of `INNER JOIN` clause . The top manager did not appear on the output.

#### 2) Comparing the rows with the same table
For example following query will return films with same length.
```sql
SELECT
    f1.title,
    f2.title,
    f1.length
FROM
    film f1
INNER JOIN film f2 ON f1.film_id <> f2.film_id
AND f1.length = f2.length;
```
### 3.4 FULL OUTER JOIN
The `full outer join` combines the results of both `left join` and `right join`.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Full-Outer-Join.png)
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Full-Outer-Join-with-Where.png)

```sql
SELECT * FROM A
FULL [OUTER] JOIN B on A.id = B.id;
```

### 3.5 CROSS JOIN
Perform the `CROSS JOIN` of two tables `T1` and `T2`. For every row from `T1` and `T2` the result set will contain a row that consists of all columns in the `T1` table followed by all columns in the `T2` table.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2016/06/PostgreSQL-CROSS-JOIN-illustration.png)


```sql
SELECT * 
FROM T1
CROSS JOIN T2;
```
It's equal to 
```
SELECT *
FROM T1
INNER JOIN T2 ON TRUE;
```
or
```
SELECT * 
FROM T1, T2;
```

### 3.6 NATURAL JOIN
A `natural join` is a join that creates an implicit join based on the same column names in the joined tables.
To use `NATURAL JOIN` `table_name` should be included in `id` like `table_name_id`.
> `NATURAL JOIN` removes duplicate fields from result.
```sql
SELECT *
FROM T1
NATURAL [INNER (default), LEFT, RIGHT] JOIN T2;
```
If you use the asterisk (*) in the select list, the result will contain the following columns:
- All the common columns, which are the columns in the both tables that have the same name
- Every column in the first and second tables that is not a common column

>`NATURAL [INNER, LEFT, RIGHT] JOIN table_name; ` is equal to `[INNER, LEFT, RIGHT] JOIN table_name USING (table_name_id); `

> ***However, you should avoid using the NATURAL JOIN whenever possible because sometimes it may cause an unexpected result***.

## 4. Grouping Data
### 4.1 GROUP BY
The `GROUP BY` clause divides the rows returned from the `SELECT` statement into groups. For each group, you can apply an `aggregate function` e.g.,  `SUM()` to calculate the sum of items or `COUNT()` to get the number of items in the groups.
> `aggregate function` created based on `GROUP BY`.
#### A) Using PostgreSQL GROUP BY without an aggregate function example
In this case, the GROUP BY works like the `DISTINCT` clause that removes duplicate rows from the result set.
#### B) Using PostgreSQL GROUP BY with AGGREGATE_FUNCTION
The `GROUP BY` clause is useful when it is used in conjunction with an `aggregate function`. For example, to get the amount that a `customer` has been paid, you use the `GROUP BY` clause to divide the `payment` table into groups; for each group, you calculate the `total` amounts using the `SUM()` function:
```sql
SELECT
	customer_id,
    other_column
	SUM (amount)
FROM
	payment
GROUP BY
	customer_id,
    other_column;
```
> some useful aggregate functions are `SUM()`, `COUNT()`

### 4.2 HAVING
We often use the `HAVING` clause in conjunction with the GROUP BY clause to filter group rows that do not satisfy a specified condition.
```sql
SELECT
	column_1,
	aggregate_function (column_2)
FROM
	tbl_name
GROUP BY
	column_1
HAVING
	condition;
```

The `HAVING` clause sets the condition for group rows created by the `GROUP BY` clause after the `GROUP BY` clause applies while the `WHERE` clause sets the condition for individual rows before `GROUP BY` clause applies. This is the main difference between the `HAVING` and `WHERE` clauses.

> The `HAVING` clause is evaluated before the `SELECT` - so the server doesn't yet know about that alias.

## 5. Performing Set Operations
The following are rules applied to the queries:
- Both queries must return the same number of columns.
- The corresponding columns in the queries must have compatible data types.

### 5.1 UNION
The `UNION` operator combines result sets of two or more SELECT statements into a single result set.
```sql
SELECT
	column_1,
	column_2
FROM
	tbl_name_1
UNION
SELECT
	column_1,
	column_2
FROM
	tbl_name_2;
```
> The UNION operator removes all duplicate rows unless the UNION ALL is used.

### 5.2 INTERSECT
The `INTERSECT` operator returns any rows that are available in both result set or returned by both queries.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2016/06/PostgreSQL-INTERSECT-Operator-300x206.png)

```sql
SELECT
	column_list
FROM
	A
INTERSECT
SELECT
	column_list
FROM
	B;
```
### 5.3 EXCEPT
The `EXCEPT` operator returns distinct rows from the first (left) query that are not in the output of the second (right) query.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2016/06/PostgreSQL-EXCEPT-300x202.png)

```sql 
SELECT column_list
FROM A
WHERE condition_a
EXCEPT 
SELECT column_list
FROM B
WHERE condition_b;
```
## 6. Grouping Sets

```sql
ELECT
    c1,
    c2,
    aggregate_function(c3)
FROM
    table_name
GROUP BY
    GROUPING SETS (
        (c1, c2),
        (c1),
        (c2),
        ()
);
```
 ** EXAMPLE **

`QUERY-1` Following query defines a grouping set of the brand and segment. It returns the number of products sold by brand and segment.
```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    segment;
```
`QUERY-2` The following query finds the number of product sold by brand. It defines a grouping set of the brand:

```
SELECT
    brand,
    NULL,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand;
```
`QUERY-3` The following query finds the number of products sold by segment. It defines a grouping set of the segment:
```
SELECT
    NULL,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    segment;
```
`QUERY-4` The following query finds the number of products sold for all brands and segments. It defines an empty grouping set.
```
SELECT
    NULL,
    NULL,
    SUM (quantity)
FROM
    sales;
```
>Because the `UNION ALL` requires all result sets to have the same number of columns with compatible data types, you need to adjust the queries by adding `NULL` to the selection list of each
```sql
QUERY-1
UNION ALL
QUERY-2
UNION ALL
QUERY-3
UNION ALL
QUERY-4
```
>Even though the code works as you expected, it has two main problems. First, it is quite lengthy. Second, it has a performance issue because PostgreSQL has to scan the sales table separately for each query.

![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-GROUPING-SETS-UNION-ALL-operator.png)

To apply this syntax to the example above, you can use GROUPING SETS instead of UNION ALL as shown in the following query:

```sql
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    GROUPING SETS (
        (brand, segment),
        (brand),
        (segment),
        ()
    );
```
#### *** Grouping function ***
The `GROUPING` function accepts a name of a column and returns bit 0 if the column is the member of the current grouping set and 1 otherwise. See the following example:
```sql
SELECT
	GROUPING(brand) grouping_brand,
	GROUPING(segment) grouping_segment,
	brand,
	segment,
	SUM (quantity)
FROM
	sales
GROUP BY
	GROUPING SETS (
		(brand, segment),
		(brand),
		(segment),
		()
	)
ORDER BY
	brand,
	segment;
```
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-GROUPING-SETS-GROUPING-function.png)

*** shorter way for GROUPING SETS is CUBE ***

```sql
CUBE(c1,c2,c3) 

GROUPING SETS (
    (c1,c2,c3), 
    (c1,c2),
    (c1,c3),
    (c2,c3),
    (c1),
    (c2),
    (c3), 
    ()
 ) 
```
*** is a special ***
```sql
ROLLUP(c1 (main column),c2,c3)

GROUPING SETS (
    (c1, c2, c3)
    (c1, c2)
    (c1)
    ()
 ) 
```
For example 
```sql
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    ROLLUP (brand (main column), segment)
ORDER BY
    brand,
    segment;
```
![](https://sp.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-ROLLUP-example.png)

## 8. Subquery
A `subquery` is a `query` nested `inside` another query such as `SELECT`, `INSERT`, `DELETE` and `UPDATE`.
- First, executes the subquery.
- Second, gets the result and passes it to the outer query.
- Third, executes the outer query.

### a) subquery with IN operator

### b) subquery with EXISTS operator
>If the subquery returns any row, the EXISTS operator returns true. If the subquery returns no row, the result of EXISTS operator is false.
The EXISTS operator only cares about the number of rows returned from the subquery, not the content of the rows, therefore, the common coding convention of EXISTS operator is as follows:
```sql
EXISTS (SELECT 1 FROM tbl WHERE condition);
```

### c) subquery with ANY operator
```sql
expression operator ANY(subquery)
```
- The `subquery` must return exactly `one` column.
- The `ANY` operator must be preceded by one of the following comparison operator =, <=, >, <, > and <>
> The `ANY` operator returns `true` if any value of the subquery meets the condition, otherwise, it returns `false`

> Note that `SOME` is a synonym for `ANY`, meaning that you can substitute `SOME` for `ANY` in any SQL statement.

*** IMPORTANT EXAMPLE ***
The following example returns the length of movie with `maximum length` for each category:
```sql
SELECT
    MAX( length )
FROM
    film
INNER JOIN film_category
        USING(film_id)
GROUP BY
    category_id;
```
Subquery in the following statement that finds the films whose lengths are greater than or equal to the maximum length of any film category (In this case it's mean minimum length in subquery result) :
```sql
SELECT title
FROM film
WHERE length >= ANY(
    SELECT MAX( length )
    FROM film
    INNER JOIN film_category USING(film_id)
    GROUP BY  category_id );
```

> The `= ANY` is equivalent to `IN` operator.

> However `x <> ANY (a,b,c)` is equivalent to `x <> a OR <> b OR x <> c`

### d) subquery with ALL operator
```sql
comparison_operator ALL (subquery)
```
- The ALL operator must be preceded by a comparison operator such as equal (=), not equal (!=), greater than (>), greater than or equal to (>=), less than (<), and less than or equal to (<=).
- The ALL operator must be followed by a subquery which also must be surrounded by the parentheses.


1. `column_name > ALL (subquery)` the expression evaluates to true if a value is `greater` than the `biggest` value returned by the subquery.
2. `column_name >= ALL (subquery)` the expression evaluates to true if a value is `greater than or equal` to the `biggest` value returned by the subquery.
3. `column_name < ALL (subquery)` the expression evaluates to true if a value is `less` than the `smallest` value returned by the subquery.
4. `column_name <= ALL (subquery)` the expression evaluates to true if a value is `less than or equal` to the `smallest` value returned by the subquery.
5. `column_name = ALL (subquery)` the expression evaluates to true if a value is `equal to any` value returned by the subquery.
6. `column_name != ALL (subquery)` the expression evaluates to true if a value is `not equal to any` value returned by the subquery.

## 8. Common Table Expressions