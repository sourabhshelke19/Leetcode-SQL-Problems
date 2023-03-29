#### Problem

Table: `Numbers`

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| num         | int  |
| frequency   | int  |
+-------------+------+
num is the primary key for this table.
Each row of this table shows the frequency of a number in the database.
```

 

The [**median**](https://en.wikipedia.org/wiki/Median) is the value separating the higher half from the lower half of a data sample.

Write an SQL query to report the **median** of all the numbers in the database after decompressing the `Numbers` table. Round the median to **one decimal point**.

The query result format is in the following example.

 

**Example 1:**

```
Input: 
Numbers table:
+-----+-----------+
| num | frequency |
+-----+-----------+
| 0   | 7         |
| 1   | 1         |
| 2   | 3         |
| 3   | 1         |
+-----+-----------+
Output: 
+--------+
| median |
+--------+
| 0.0    |
+--------+
Explanation: 
If we decompress the Numbers table, we will get [0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3], so the median is (0 + 0) / 2 = 0.
```







#### Solution

##### <u>Approach</u>

We can solve it using SUM() as a window function on frequency inside a subquery.



#### Code and Explanation

```sql
SELECT AVG(num) as median
FROM (SELECT num, frequency, 
     SUM(frequency) OVER(ORDER BY num) freq, 
     (SUM(frequency) OVER())/2 median_num
     FROM Numbers
     )t
WHERE median_num BETWEEN (freq - frequency) AND freq;
```



First, let's have a look at the subquery:

```sql
SELECT num, frequency, 
     SUM(frequency) OVER(ORDER BY num) freq, 
     (SUM(frequency) OVER())/2 median_num
     FROM Numbers
```

We are returning the original *num* and *frequency*. Additionally, we are also getting a cumulative frequency as *freq* and the *median_num* is nothing but the *sum(frequency)/2*
We get the below table:

| num  | frequency | freq | median_num |
| ---- | --------- | ---- | ---------- |
| 0    | 7         | 7    | 6          |
| 1    | 1         | 8    | 6          |
| 2    | 3         | 11   | 6          |
| 3    | 1         | 12   | 6          |

After getting this output from subquery, we will filter out one number which is exactly at the midpoint of all numbers if we have odd total numbers OR we will filter out two numbers which are exactly at the midpoint of all numbers if we have even total numbers
(Example: EVEN - 5, 6 would be at center if we have numbers 1 through 10
ODD - 5 would be at center if we have numbers 1 through 9)

In our case:
*WHERE median_num BETWEEN (freq - frequency) AND freq;*
will return num = 0 because for num 0, ***median_num\* = 6 lies between**
***(freq - frequency)\* = 7-7 = 0** AND ***freq\* = 7**

Finally we take **AVG(num)** of the num which we filtered out in the where clause to get our output. In our case we got 0.