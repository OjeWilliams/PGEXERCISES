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

-- Second attempt regualr expression
SELECT memid, telephone FROM cd.members
WHERE telephone ~ '[()]' ;

```
\
5.The zip codes in our example dataset have had leading zeroes removed from them by virtue of being stored as a numeric type. Retrieve all zip codes from the members table, padding any zip codes less than 5 characters long with leading zeroes. Order by the new zip code.
```

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
