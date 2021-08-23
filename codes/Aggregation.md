All the questions for this section can be found [here.](https://pgexercises.com/questions/aggregates/)

\
1.For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count.

```
SELECT COUNT(*) FROM cd.facilities ;
```
\
2.Produce a count of the number of facilities that have a cost to guests of 10 or more.
```
SELECT COUNT(*) FROM cd.facilities
WHERE guestcost >= 10 ;
```

\
3.Produce a count of the number of recommendations each member has made. Order by member ID.
```
SELECT recommendedby, COUNT(*) FROM cd.members
WHERE recommendedby IS NOT NULL 
GROUP BY recommendedby
ORDER BY  recommendedby;
```
\
4.Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id.
```
SELECT facid, SUM(slots) AS Total_Slots FROM cd.bookings
GROUP BY facid 
ORDER BY facid;

```
\
5.Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.
```
SELECT facid, SUM(slots) AS "Total Slots" FROM cd.bookings
WHERE starttime >= '2012-09-01' 
AND starttime < '2012-10-01'
GROUP BY facid 
ORDER BY SUM(slots);

```

\
6.Produce a list of the total number of slots booked per facility per month in the year of 2012. Produce an output table consisting of facility id and slots, sorted by the id and month.

```
SELECT facid, EXTRACT(MONTH FROM starttime) AS MONTH,
SUM(slots) AS "Total Slots" 
FROM cd.bookings
WHERE EXTRACT(YEAR FROM starttime) = 2012 
GROUP BY facid, month
ORDER BY facid, month ;

OR

-- Taking inspiration from question 5

SELECT facid, EXTRACT(MONTH FROM starttime) AS MONTH,
SUM(slots) AS "Total Slots" 
FROM cd.bookings
WHERE starttime >= '2012-01-01' 
AND starttime < '2012-12-31'
GROUP BY facid, month
ORDER BY facid, month ;
```

\
7.Find the total number of members (including guests) who have made at least one booking.
```
SELECT COUNT(DISTINCT(memid)) FROM cd.bookings;
```

\
8.Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and slots, sorted by facility id
```
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid ;
```

\
9.Produce a list of facilities along with their total revenue. The output table should consist of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!
```
-- needed some help with this 
SELECT fac.name, 
SUM(slots * CASE
	        WHEN memid = 0 THEN fac.guestcost
			ELSE fac.membercost
			END) AS revenue
FROM cd.bookings AS book
JOIN cd.facilities AS fac 
ON book.facid = fac.facid
GROUP BY fac.name
ORDER BY revenue
```
\
10.Produce a list of facilities with a total revenue less than 1000. Produce an output table consisting of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!
```
-- this was also challenging
SELECT name, revenue  
FROM (SELECT fac.name, SUM( CASE WHEN memid = 0 THEN slots * guestcost 
	         ELSE slots * membercost
			 END
  ) AS revenue 
FROM cd.bookings AS book 
JOIN cd.facilities AS fac
ON fac.facid = book.facid
GROUP BY fac.name) AS grp
WHERE revenue < 1000	  
ORDER BY revenue ;

another solution including HAVING

select facs.name, sum(case 
		when memid = 0 then slots * facs.guestcost
		else slots * membercost
	end) as revenue
	from cd.bookings bks
	inner join cd.facilities facs
		on bks.facid = facs.facid
	group by facs.name
	having sum(case 
		when memid = 0 then slots * facs.guestcost
		else slots * membercost
	end) < 1000
order by revenue;


```
\
11.Output the facility id that has the highest number of slots booked. For bonus points, try a version without a LIMIT clause. This version will probably look messy!
```
SELECT facid, SUM(slots) AS "Total SLots" 
FROM cd.bookings
GROUP BY facid
ORDER BY SUM(slots) DESC
LIMIT 1;

-- WITRH CTE
WITH maxy AS(
  	      SELECT facid, SUM(slots) AS total_slots FROM cd.bookings
  	      GROUP BY facid
             )
SELECT facid, total_slots
FROM maxy
WHERE total_slots = (SELECT MAX(total_slots) FROM maxy);
```

\
12.Produce a list of the total number of slots booked per facility per month in the year of 2012. In this version, include output rows containing totals for all months per facility, and a total for all months for all facilities. The output table should consist of facility id, month and slots, sorted by the id and month. When calculating the aggregated values for all months and all facids, return null values in the month and facid columns.

```
-- First attempt, it seems I need to incorporate the total count for each month after their individual counts
SELECT facid, EXTRACT(MONTH FROM starttime) AS "Month", SUM(slots) AS slots
FROM cd.bookings
WHERE starttime BETWEEN '2012-01-01' AND '2012-12-31'
GROUP BY facid, "Month"
ORDER BY facid, "Month" ;

OR

-- Used grouping set commands... ie ROLLUP . note the order of items inside rollu matters

SELECT facid, EXTRACT(MONTH FROM starttime) AS "Month", SUM(slots) AS slots
FROM cd.bookings
WHERE starttime BETWEEN '2012-01-01' AND '2012-12-31'
GROUP BY ROLLUP(facid, "Month")
ORDER BY facid, "Month" ;

OR
-- one of their solutions with a CTE and Union All
with bookings as (
	select facid, extract(month from starttime) as month, slots
	from cd.bookings
	where
		starttime >= '2012-01-01'
		and starttime < '2013-01-01'
)
select facid, month, sum(slots) from bookings group by facid, month
union all
select facid, null, sum(slots) from bookings group by facid
union all
select null, null, sum(slots) from bookings
order by facid, month;
```

\
13.Produce a list of the total number of hours booked per facility, remembering that a slot lasts half an hour. The output table should consist of the facility id, name, and hours booked, sorted by facility id. Try formatting the hours to two decimal places.
```
-- First attempt

SELECT fac.facid, fac.name, ROUND(SUM(book.slots*2),2) AS total_hours
FROM cd.bookings AS book
JOIN cd.facilities AS fac ON book.facid = fac.facid
GROUP BY fac.facid, fac.name
ORDER BY fac.facid ;

-- I realised the issue I needed to divide by 2 not multiply. Also it has to be 2.0 for floating point division
SELECT fac.facid, fac.name, ROUND(SUM(book.slots/2.0),2) AS total_hours
FROM cd.bookings AS book
JOIN cd.facilities AS fac ON book.facid = fac.facid
GROUP BY fac.facid, fac.name
ORDER BY fac.facid ;

-- Their answer
select facs.facid, facs.name,
	trim(to_char(sum(bks.slots)/2.0, '9999999999999999D99')) as "Total Hours"

	from cd.bookings bks
	inner join cd.facilities facs
		on facs.facid = bks.facid
	group by facs.facid, facs.name
order by facs.facid;  

```

\
14.Produce a list of each member name, id, and their first booking after September 1st 2012. Order by member ID.
```
SELECT DISTINCT mem.surname, mem.firstname, mem.memid, MIN(starttime) AS starttime
FROM cd.members AS mem
JOIN cd.bookings AS book ON mem.memid = book.memid
WHERE starttime >= '2012-09-01'
GROUP BY  mem.surname, mem.firstname, mem.memid
ORDER BY mem.memid ;

```
\
15.Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id.
```


```
