#### Problem

Table: `Product`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
product_id is the primary key of this table.
Each row of this table indicates the name and the price of each product.
```

Table: `Sales`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+-------------+---------+
This table has no primary key, it can have repeated rows.
product_id is a foreign key to the Product table.
Each row of this table contains some information about one sale.
```

 

Write an SQL query that reports the best **seller** by total sales price, If there is a tie, report them all.

Return the result table in **any order**.

The query result format is in the following example.

 

**Example 1:**

```
Input: 
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
Output: 
+-------------+
| seller_id   |
+-------------+
| 1           |
| 3           |
+-------------+
Explanation: Both sellers with id 1 and 3 sold products with the most total price of 2800.
```





#### Solution

##### <u>Approach</u>

I have solved it using two ways: -

1. Use Subquery to find all *seller_ids* **HAVING SUM(price) = MAX of SUM(PRICE)**.
2. Using **RANK()** without partition and ordering it by *SUM(Price)* in descending order. Don't forget to **GROUP** **BY** *seller_id* in the subquery.
   This one is faster.



#### Code

```sql
# Write your MySQL query statement below
SELECT project_id 
FROM (
  SELECT project_id, COUNT(project_id),
  RANK() OVER(ORDER BY COUNT(project_id) DESC) as cnt
  FROM Project
  GROUP BY project_id
) t
WHERE t.cnt = 1;
```