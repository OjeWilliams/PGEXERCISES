All the questions for this section can be found [here.](https://pgexercises.com/questions/joins/)

1.How can you produce a list of the start times for bookings by members named 'David Farrell'?

```
SELECT start.starttime FROM cd.bookings AS start
JOIN cd.members AS members 
ON start.memid = members.memid
WHERE members.firstname = 'David' 
AND members.surname = 'Farrell' ;
```











