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
