All the questions for this section can be found [here.](https://pgexercises.com/questions/joins/)
\
1.How can you produce a list of the start times for bookings by members named 'David Farrell'?

```
-- My first attempt
SELECT start.starttime 
FROM cd.bookings AS start, cd.members AS members 
WHERE start.memid = members.memid
AND members.firstname = 'David' 
AND members.surname = 'Farrell' ;


-- Using Join
SELECT start.starttime FROM cd.bookings AS start
JOIN cd.members AS members 
ON start.memid = members.memid
WHERE members.firstname = 'David' 
AND members.surname = 'Farrell' ;
```
\
2.How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.
```
SELECT book.starttime AS start, fac.name AS name 
FROM cd.bookings AS book
JOIN cd.facilities AS fac
ON book.facid = fac.facid
WHERE book.starttime >= '2012-09-21'
AND book.starttime < '2012-09-22'
AND fac.name LIKE 'Tennis%'
-- AND fac.name IN ('Tennis Court 1', 'Tennis Court 2') this should also work
ORDER BY book.starttime;
```

\
3. How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname).
```
-- Used subquery and inner join
SELECT DISTINCT(A.firstname) AS firstname, A.surname AS surname
FROM (SELECT Anames.firstname, Anames.surname FROM cd.members AS Anames
	  JOIN cd.members AS Bnames on
	  Anames.memid = Bnames.recommendedby
	   ) AS A
ORDER BY A.surname, A.firstname ;

OR
-- no subquery
SELECT DISTINCT(Anames.firstname) AS firstname, Anames.surname AS surname
FROM cd.members AS Anames
	  JOIN cd.members AS Bnames on
	  Anames.memid = Bnames.recommendedby
ORDER BY Anames.surname, Anames.firstname ;
```
 












