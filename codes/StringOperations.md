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
-- Had to look up padding. The function is LPAD in Postgresql LPAD('string',
SELECT LPAD(cast(zipcode AS CHAR ), 5,'0') AS zip FROM cd.members
ORDER BY zip;

-- had to specify how many CHAR
SELECT LPAD(cast(zipcode AS CHAR(5)), 5,'0') AS zip FROM cd.members
ORDER BY zip;
```
\
6.You'd like to produce a count of how many members you have whose surname starts with each letter of the alphabet. Sort by the letter, and don't worry about printing out a letter if the count is 0.
```
-- First Attempt
SELECT SUBSTR(surname,1,1) AS letter, COUNT(*) FROM cd.members
GROUP BY letter
ORDER BY letter ;

-- Second Attempt
SELECT LEFT(surname,1) AS letter, COUNT(*) FROM cd.members
GROUP BY letter
ORDER BY letter ;
```
\
7.The telephone numbers in the database are very inconsistently formatted. You'd like to print a list of member ids and numbers that have had '-','(',')', and ' ' characters removed. Order by member id.
```
-- After some research I came across REPLACE() and REGEXP_REPLACE() https://www.postgresqltutorial.com/postgresql-replace/
-- At the same link above there is also an explanation for a function .. TRANSLATE()

-- First Attempt
SELECT memid, REGEXP_REPLACE(telephone,'[^0-9]', '', 'g') AS telephone
FROM cd.members
ORDER by memid ;

-- Second Attempt


```
