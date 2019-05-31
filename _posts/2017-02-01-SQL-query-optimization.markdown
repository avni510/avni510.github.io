---
layout: post
title:  SQL Query Optimization
date:   2017-02-01 10:00:00 -0600
---
This blogpost will specifically cover cross joins and how they can lead to slow queries. Cross joins also known as a Cartesian Product joins all rows of each table together. The wikipedia page on a [Cartesian Product](https://en.wikipedia.org/wiki/Cartesian_product) has a good visual of what that would be mean. 

![Cartesian_Product](/assets/cartesian_product.png)

Lets say you had a table A that has rows `x, y, z` and another table B that has rows `1, 2, 3`. A cross join would mean row `x` from table A would join with rows `1, 2, and 3` from table B and row `y` from table A would join with rows `1, 2, 3` from table B, etc. The squares in the visual above can be thought of as the number of resulting rows. 

This causes the number of rows in your resulting table to be equal to the number of rows in Table A * number of rows in Table B. In the example above 3 X 3. 

If you are working on tables with a large number of rows this can cause your queries to slow down significantly because the result set is so large. 

Here is an example of when a cross join maybe be used in a small dataset

Lets say we have a `LOGTIMES` table and `TIMECODES` table

![logtimes_table](/assets/LogTimesTable.png)

![timecodes_table](/assets/TImecodesTable.png)

And we want all the timecodes in `logtimes` by their name instead of by id for employee id = 1. 

Here's one way to solve it with a cross join

```
SELECT LogTimesTimecodes.* FROM (
  SELECT lt.id, lt.emp_id, lt.date, lt.hours_worked, t.timecode 
  FROM LOGTIMES lt FULL JOIN TIMECODES t on lt.timecode_id = t.id 
  WHERE lt.id IS NOT NULL) as LogTimesTimecodes 
WHERE LogTimesTimecodes.emp_id = 1
```

This query is taking the two tables `LOGTIMES` (lt) and `TIMECODES` (t) and matching each row where `LOGTIMES` timecode_id column equals `TIMECODES` id column. And it is throwing out all rows where `LOGTIMES` id value is NULL. This query results in another table called LogTimesTimecodes and I filter this table to only include rows that have an employee id = 1. 

The resulting table will be this 

![table_after_cross_join](/assets/table_after_cross_join.png)

Here is another way to write the same query and this way uses an inner join

```
SELECT LogTimesTimecodes.* FROM (
  SELECT lt.id, lt.emp_id, lt.date,
  lt.hours_worked, t.timecode
  FROM LOGTIMES lt INNER JOIN TIMECODES t on
  lt.timecode_id=t.id)
  as LogTimesTimecodes
WHERE LogTimesTimecodes.id=1
 )
```
This will produce the same table

![table_after_cross_join](/assets/table_after_cross_join.png)

Although my database is not that large the more efficent way to run this query would be the second way (the INNER JOIN). This is because the cross join initially produced a table with 5 rows (if no join clause was specified 9 rows would be the result table) and then was filtered down to ignore the NULL values. The second query creates a table with a smaller amount of rows because only the rows that have matching id values are stitched together. The key difference between the 2 queries is the subquery 

```
  SELECT lt.id, lt.emp_id, lt.date, lt.hours_worked, t.timecode 
  FROM LOGTIMES lt FULL JOIN TIMECODES t on lt.timecode_id = t.id 
  WHERE lt.id IS NOT NULL) as LogTimesTimecodes 
```
and

```
  SELECT lt.id, lt.emp_id, lt.date,
  lt.hours_worked, t.timecode
  FROM LOGTIMES lt INNER JOIN TIMECODES t on
  lt.timecode_id=t.id)
  as LogTimesTimecodes
```
Without the `WHERE` clause the first query would produce a total of 5 rows which is then filtered down and a table (LogTimesTimecodes) is returned. Where as the second query will only populate the new table formed (LogTimesTimecodes) if the id field from `LOGTIMES` equals the id field from `TIMECODES`. When translating this to a large database the cross join query could take a very long time to run depending on the tables sizes. 
