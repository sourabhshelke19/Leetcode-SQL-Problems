#### Problem

Table: `Events`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| business_id   | int     |
| event_type    | varchar |
| occurences    | int     | 
+---------------+---------+
(business_id, event_type) is the primary key of this table.
Each row in the table logs the info that an event of some type occurred at some business for a number of times.
```

 

The **average activity** for a particular `event_type` is the average `occurences` across all companies that have this event.

An **active business** is a business that has **more than one** `event_type` such that their `occurences` is **strictly greater** than the average activity for that event.

Write an SQL query to find all **active businesses**.

Return the result table in **any order**.

The query result format is in the following example.

 

**Example 1:**

```
Input: 
Events table:
+-------------+------------+------------+
| business_id | event_type | occurences |
+-------------+------------+------------+
| 1           | reviews    | 7          |
| 3           | reviews    | 3          |
| 1           | ads        | 11         |
| 2           | ads        | 7          |
| 3           | ads        | 6          |
| 1           | page views | 3          |
| 2           | page views | 12         |
+-------------+------------+------------+
Output: 
+-------------+
| business_id |
+-------------+
| 1           |
+-------------+
Explanation:  
The average activity for each event can be calculated as follows:
- 'reviews': (7+3)/2 = 5
- 'ads': (11+7+6)/3 = 8
- 'page views': (3+12)/2 = 7.5
The business with id=1 has 7 'reviews' events (more than 5) and 11 'ads' events (more than 8), so it is an active business.
```





#### Approach

Using AVG window function

#### Code

```sql
# Write your MySQL query statement below

SELECT e1.business_id 
FROM (
  SELECT business_id, event_type, occurences,
    AVG(occurences) OVER(PARTITION BY event_type) as avr
  FROM Events
) e1
INNER JOIN Events e2
ON e1.business_id = e2.business_id AND e1.event_type = e2.event_type
WHERE e2.occurences > e1.avr
GROUP BY e1.business_id
HAVING COUNT(DISTINCT e1.event_type) > 1;
```

#### Explanation

Firstly, lets understand the subquery.

```swift
SELECT business_id, event_type, occurences,
    AVG(occurences) OVER(PARTITION BY event_type) as avr
  FROM Events
```

This gives below output:

| business_id | event_type | avr  |
| ----------- | ---------- | ---- |
| 1           | ads        | 8    |
| 2           | ads        | 8    |
| 3           | ads        | 8    |
| 1           | page views | 7.5  |
| 2           | page views | 7.5  |
| 1           | reviews    | 5    |
| 3           | reviews    | 5    |

Here we simply added a column of average number of event_type for each type of event against business_id and event_type. Now we will just join this result with another Events table to compare the occurences of each event for a business_id against the average.
