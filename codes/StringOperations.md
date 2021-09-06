All questions can be found [here.](https://pgexercises.com/questions/string/) <br>
\
1.Output the names of all members, formatted as 'Surname, Firstname'
```
-- First attempt
SELECT CONCAT(surname, ', ' ,firstname) AS name FROM cd.members ;

-- Second Attmept 
SELECT surname || ', ' || firstname AS name FROM cd.members ;

-- Third Attempt
SELECT CONCAT_WS (', ', surname, firstname) AS name FROM cd.members ;
```
\
2.Find all facilities whose name begins with 'Tennis'. Retrieve all columns.?
```
SELECT * FROM cd.facilities
WHERE name LIKE 'Tennis%' ;
```
\
3.Perform a case-insensitive search to find all facilities whose name begins with 'tennis'. Retrieve all columns.
```
-- First Attempt
SELECT * FROM cd.facilities WHERE name ILIKE 'Tennis%' ;

-- Their answer 
select * from cd.facilities where upper(name) like 'TENNIS%'; 
```
\
4.You've noticed that the club's member table has telephone numbers with very inconsistent formatting. You'd like to find all the telephone numbers that contain parentheses, returning the member ID and telephone number sorted by member ID.
```
First attempt
SELECT memid, telephone FROM cd.members
WHERE telephone SIMILAR TO '%[()]%' ;

```
\
5.How can you produce a list of all facilities with the word 'Tennis' in their name?
```
SELECT * FROM cd.facilities
WHERE name LIKE '%Tennis%' ;
```
\
6.How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.
```
SELECT * FROM cd.facilities
WHERE facid IN (1,5) ;
```
\
7.How can you produce a list of facilities, with each labelled as 'cheap' or 'expensive' depending on if their monthly maintenance cost is more than $100? Return the name and monthly maintenance of the facilities in question.
```
SELECT name,
CASE
	WHEN monthlymaintenance > 100 THEN 'expensive'
	ELSE 'cheap'
END AS COST
FROM cd.facilities;
```
