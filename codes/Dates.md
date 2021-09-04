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
3.Produce a list of all the dates in October 2012. They can be output as a timestamp (with time set to midnight) or a date.
```
SELECT GENERATE_SERIES(TIMESTAMP '2012-10-01 00:00:00', TIMESTAMP '2012-10-31 00:00:00',INTERVAL '1 day') AS TS ;
```

\
4.Get the day of the month from the timestamp '2012-08-31' as an integer.
```
SELECT EXTRACT(DAY FROM TIMESTAMP'2012-08-31') ;
```

\
5.Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00'
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
6.For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type.
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
7.For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value.
```
SELECT (DATE_TRUNC('MONTH', time.test) + INTERVAL '1 MONTH')
       - DATE_TRUNC('DAY' , time.test) as left_to_go
FROM ( SELECT TIMESTAMP '2012-02-11 01:00:00' AS test) AS time ;
```
\
8.Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system.
```
-- Be careful with this one. When you used the ORDER BY remy that which colums goes first affects the output.
SELECT starttime, starttime + slots * (INTERVAL '30 minutes') AS endtime
FROM cd.bookings
ORDER BY endtime DESC, starttime DESC 
LIMIT 10;
```

\
9.Work out the utilisation percentage for each facility by month, sorted by name and month, rounded to 1 decimal place. Opening time is 8am, closing time is 8.30pm. You can treat every month as a full month, regardless of if there were some dates the club was not open.
```
-- I had alot of problems with this one. Had to look back at q6 to see how I dealt with substracting months to find the days of each month
-- First attempt. This gave an error regarding timestamps and I suggested I added explicit type casts
SELECT name, mymonth, ROUND((month_total)/(mymonth + INTERVAL '1 month') - mymonth,1)
FROM(
SELECT fac.name AS name, DATE_TRUNC('month', starttime) AS mymonth, SUM(slots) AS month_total 
FROM cd.bookings AS book
JOIN cd.facilities AS fac ON book.facid = fac.facid
GROUP BY name, mymonth
) AS my_util
ORDER BY name;

--Second Attempt. After messing around with casting, this was the result. Still not correct, the numbers are off. Specifically mu itlization numbers are all greater than 1
-- when they should all by decimals because I havent multiplied by 100 yet to get percentage.
SELECT name, mymonth, ROUND((month_total)/CAST(
                          CAST((mymonth + INTERVAL '1 month') AS DATE)
			- CAST(mymonth AS DATE) AS numeric),1) AS utilization

FROM(
		SELECT fac.name AS name, DATE_TRUNC('month', starttime) AS mymonth, SUM(slots) AS month_total 
		FROM cd.bookings AS book
		JOIN cd.facilities AS fac ON book.facid = fac.facid
		GROUP BY name, mymonth
    ) AS my_util
    
-- Correct attempt. I realised I was not accounting for the for how many hours in the day were available. It opens at 8am and closes at 8:30pm.. thats 12.5 hours therefore 
-- 25 half an hour slots. recall that the slots are half an hour increments!!!
SELECT name, mymonth, 
ROUND(100*(month_slots)/CAST(
                         25 * (CAST((mymonth + INTERVAL '1 month') AS DATE)
			      - CAST(mymonth AS DATE)) AS numeric),1) AS utilization

FROM(
      SELECT fac.name AS name, DATE_TRUNC('month', starttime) AS mymonth, SUM(slots) AS month_slots
      FROM cd.bookings AS book
      JOIN cd.facilities AS fac ON book.facid = fac.facid
      GROUP BY name, mymonth
    ) AS my_util
	
ORDER BY name;
	
ORDER BY name;

```
\
2.Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00'
```
SELECT TIMESTAMP'2012-08-31 01:00:00' - TIMESTAMP'2012-07-30 01:00:00' AS INTERVAL ;

```

