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












