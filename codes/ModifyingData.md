All the questions for this section can be found [here.](https://pgexercises.com/questions/updates/)
\
1.The club is adding a new facility - a spa. We need to add it into the facilities table. Use the following values: \
facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```
INSERT INTO cd.facilities
	(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES (9, 'Spa', 20, 30, 100000, 800) ;

-- if you are putting a value into each column you could have jsut done this
INSERT INTO cd.facilities VALUES (9, 'Spa', 20, 30, 100000, 800) ;

```
\
2.In the previous exercise, you learned how to add a facility. Now you're going to add multiple facilities in one command. Use the following values: \
facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800. \
facid: 10, Name: 'Squash Court 2', membercost: 3.5, guestcost: 17.5, initialoutlay: 5000, monthlymaintenance: 80.
```
INSERT INTO cd.facilities
  (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
VALUES (9, 'Spa', 20, 30, 100000, 800),
       (10, 'Squash Court 2', 3.5, 17.5, 5000, 80) ;
       
-- could have also been done like this

INSERT INTO cd.facilities
    (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
SELECT 9, 'Spa', 20, 30, 100000, 800
UNION ALL
    SELECT 10, 'Squash Court 2', 3.5, 17.5, 5000, 80;
```

\
3.Let's try adding the spa to the facilities table again. This time, though, we want to automatically generate the value for the next facid, rather than specifying it as a constant. Use the following values for everything else: \
Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.
```
INSERT INTO cd.facilities
    (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
SELECT (SELECT MAX(facid) + 1 FROM cd.facilities), 'Spa', 20, 30, 100000, 800 ;
```
\
4.We made a mistake when entering the data for the second tennis court. The initial outlay was 10000 rather than 8000: you need to alter the data to fix the error.
```
-- needed to check the position(actually the id) of the second tennis court and use it to specify where the change must happen
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE cd.facilities.facid = 1 ;
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

\
8.The Produce a list of costly bookings exercise contained some messy logic: we had to calculate the booking cost in both the WHERE clause and the CASE statement. Try to simplify this calculation using subqueries. For reference, the question was:
How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost.
```
-- this is basically what was done in question 6 but now with a subquery.
SELECT member, facility, cost 
FROM ( -- this should set up member, facility and cost appropriately
	SELECT CONCAT(mem.firstname,' ',mem.surname) AS member, fac.name AS facility,
	CASE WHEN mem.memid = 0 THEN fac.guestcost * book.slots
     	     ELSE fac.membercost * book.slots
	END AS cost
  	FROM cd.members AS mem
  	JOIN cd. bookings AS book ON mem.memid = book.memid
  	JOIN cd.facilities AS fac ON book.facid = fac.facid
  	WHERE book.starttime >= '2012-09-14' AND book.starttime < '2012-09-15'  
     ) AS costly
-- now I can just filter by the cost amount we desire then order it in descending
WHERE cost > 30
ORDER BY cost DESC ;
```

