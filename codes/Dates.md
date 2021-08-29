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
4.How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname).
```
SELECT 
    A.firstname AS memFname, A.surname AS memSname,
	B.firstname AS recFname, B.surname AS recSname
FROM cd.members AS A
LEFT OUTER JOIN cd.members AS B
ON B.memid = A.recommendedby
ORDER BY memsname, memfname ;
```
\
5.How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name.
```
-- First attempt
SELECT DISTINCT firstname ||' '|| surname AS member, name
FROM cd.members
JOIN cd.bookings ON cd.members.memid = cd.bookings.memid
JOIN cd.facilities ON cd.bookings.facid = cd.facilities.facid
WHERE name LIKE 'Tennis%'
ORDER BY member, name;

--more careful attempt
SELECT DISTINCT CONCAT(mem.firstname,' ',mem.surname) AS member, fac.name
FROM cd.members AS mem
JOIN cd.bookings AS book ON mem.memid = book.memid
JOIN cd.facilities AS fac ON book.facid = fac.facid
WHERE name LIKE 'Tennis%'
ORDER BY member, name;
```

\
6.How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries.

```
-- almost forgot about slots, which involved how many half an hour sessions they booked and remember we need costs greater than 30
-- this one was more involved since we had to do some calcs but alos had to select our requirements carefully with no subqueries

SELECT CONCAT(mem.firstname,' ',mem.surname) AS member, fac.name AS facility,
CASE WHEN mem.memid = 0 THEN fac.guestcost * book.slots
     ELSE fac.membercost * book.slots
END AS cost
FROM cd.members AS mem
JOIN cd.bookings AS book ON mem.memid = book.memid
JOIN cd.facilities AS fac ON book.facid = fac.facid
WHERE book.starttime >= '2012-09-14' AND book.starttime < '2012-09-15' 
AND (mem.memid = 0 AND fac.guestcost * book.slots >30 
	 OR mem.memid != 0 AND fac.membercost * book.slots >30 
	 )
ORDER BY cost DESC;
```
\
7.How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered.
```
-- this one also a little tricky remember to use distinct and be careful with the subquery

SELECT DISTINCT CONCAT(mem.firstname,' ',mem.surname) AS member,
   (SELECT CONCAT(recos.firstname,' ',recos.surname) AS recommender	   
    FROM cd.members AS recos
	WHERE recos.memid = mem.recommendedby
	)
FROM cd.members AS mem
ORDER BY member ;
```
