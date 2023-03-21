#### Problem

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
id is the primary key for this table.
id is an autoincrement column.
```

Write an SQL query to find all numbers that appear at least three times consecutively.

Return the result table in **any order**.

The query result format is in the following example.

 

**Example 1:**

```
Input: 
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+
Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
Explanation: 1 is the only number that appears consecutively for at least three times.
```







#### Solution

##### <u>Approach</u>

Using *LAG* and *LEAD* to get the numbers before and after the current number



**# Explanation**



```sql
SELECT DISTINCT num as ConsecutiveNums
FROM (
     SELECT id, num, 
    LAG(num) OVER(ORDER BY id) as prev_num,
    LEAD(num) OVER(ORDER BY id) as next_num
    FROM Logs
 ) l
 WHERE l.num = l.prev_num
 AND l.prev_num = l.next_num
 AND l.num = l.next_num;
```

Since, we want to find a number appearing three consecutive times, we need to select a particular number whose previous and next numbers are the same.



```sql
SELECT id, num, 

	LAG(num) OVER(ORDER BY id) as prev_num,

	LEAD(num) OVER(ORDER BY id) as next_num

FROM Logs
```



This part of code will give us the selected columns. You can verify your output is correct by checking if **prev_num** for the first num and **next_num** for the last num are **NULL**.



Then, we simply need to find numbers that have the same value in three columns i.e., **num**, **prev_num** and **next_num**. Put **DISTINCT** keyword in the outer **SELECT** statement to get unique values.



Check out LAG and LEAD functions on this URL:

https://learnsql.com/blog/lead-and-lag-functions-in-sql/