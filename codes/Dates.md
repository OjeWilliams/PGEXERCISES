All the questions for this section can be found [here.](https://pgexercises.com/questions/date/)

\
1.Produce a timestamp for 1 a.m. on the 31st of August 2012.
```
SELECT TIMESTAMP '2012-08-31 01:00:00' ;

OR

select '2012-08-31 01:00:00'::timestamp;
select cast('2012-08-31 01:00:00' as timestamp);
```
\
2.Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00'
```
SELECT TIMESTAMP'2012-08-31 01:00:00' - TIMESTAMP'2012-07-30 01:00:00' AS INTERVAL ;

```

\
3.Get the day of the month from the timestamp '2012-08-31' as an integer.
```
SELECT EXTRACT(DAY FROM TIMESTAMP'2012-08-31') ;
```
\
4.Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00'
```
-- first attempt
  SELECT ((DATE_PART('day', '2012-09-02 00:00:00'::timestamp - '2012-08-31 01:00:00'::timestamp) * 24 + 
                DATE_PART('hour', '2012-09-02 00:00:00'::timestamp - '2012-08-31 01:00:00'::timestamp)) * 60 +
                DATE_PART('minute', '2012-09-02 00:00:00'::timestamp - '2012-08-31 01:00:00'::timestamp)) * 60 +
                DATE_PART('second', '2012-09-02 00:00:00'::timestamp - '2012-08-31 01:00:00'::timestamp);
                
-- second attempt
SELECT EXTRACT(DAY FROM ts.int)*60*60*24 +
	EXTRACT(HOUR FROM ts.int)*60*60 + 
	EXTRACT(MINUTE FROM ts.int)*60 +
	EXTRACT(SECOND FROM ts.int)
	FROM
		(SELECT TIMESTAMP '2012-09-02 00:00:00' - '2012-08-31 01:00:00' AS int) ts
		
-- Their solution. much simplier
SELECT EXTRACT(EPOCH FROM (TIMESTAMP '2012-09-02 00:00:00' - '2012-08-31 01:00:00')); 

```
\
5.For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type.
```

```

\
6.How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries.

```

```
\
7.How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered.
```

```
