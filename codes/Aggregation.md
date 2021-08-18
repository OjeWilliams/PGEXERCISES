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
-- needed to check the position(actually the id) of the second tennis court and use it to specify where the change must happen
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE cd.facilities.facid = 1 ;
```
\
5.We want to increase the price of the tennis courts for both members and guests. Update the costs to be 6 for members, and 30 for guests.
```
-- recall that there are 2 tewnnis courts and they take the first 2 facid values
UPDATE cd.facilities
SET membercost = 6, guestcost = 30  
WHERE facid = 0 OR facid = 1 ;

-- another way 
UPDATE cd.facilities
SET membercost = 6, guestcost = 30  
WHERE facid IN (0,1) ;
```

\
6.We want to alter the price of the second tennis court so that it costs 10% more than the first one. Try to do this without using constant values for the prices, so that we can reuse the statement if we want to.

```
UPDATE cd.facilities AS fac1
SET membercost = (select 1.1 * fac1.membercost),
    guestcost =  (select 1.1 * fac1.guestcost)
FROM (SELECT * FROM cd.facilities WHERE facid = 0) AS fac2
WHERE fac1.facid = 1 ;

OR

UPDATE cd.facilities fac
SET membercost = (select 1.1 * membercost FROM cd.facilities WHERE facid = 0),
    guestcost =  (select 1.1 * guestcost  FROM cd.facilities WHERE facid = 0)
WHERE fac.facid = 1 ;
```
\
7.As part of a clearout of our database, we want to delete all bookings from the cd.bookings table. How can we accomplish this?
```
DELETE FROM cd.bookings;
```

\
8.We want to remove member 37, who has never made a booking, from our database. How can we achieve that?
```
DELETE FROM cd.members
WHERE memid = 37 ;
```

\
9.In our previous exercises, we deleted a specific member who had never made a booking. How can we make that more general, to delete all members who have never made a booking?
```
DELETE FROM cd.members
WHERE memid NOT IN (SELECT memid FROM cd.bookings);
```

