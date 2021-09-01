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
-- this took longer than expected. Remember also that when you substract timestamps it returns days as the output
SELECT EXTRACT(MONTH FROM mymonth.month) AS Month,
       (mymonth.month + INTERVAL '1 month') - mymonth.month  AS Length
FROM
	(
	SELECT GENERATE_SERIES(TIMESTAMP '2012-01-01', TIMESTAMP '2012-12-01', INTERVAL'1 month') as month
	) mymonth
ORDER BY Month ;

```

\
6.For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value.
```
SELECT (DATE_TRUNC('MONTH', time.test) + INTERVAL '1 MONTH')
       - DATE_TRUNC('DAY' , time.test) as left_to_go
FROM ( SELECT TIMESTAMP '2012-02-11 01:00:00' AS test) AS time ;
```
\
7.Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system.
```

```
