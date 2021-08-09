All questions can be found [here.](https://pgexercises.com/questions/basic/)
\
1.How can you retrieve all the information from the cd.facilities table?

```
SELECT * FROM cd.facilities ;
```
\
2.You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs?

```
SELECT name, membercost FROM cd.facilities ;
```
\
3.How can you produce a list of facilities that charge a fee to members?
```
SELECT * FROM cd.facilities
WHERE membercost > 0 ;
```
\
4.How can you produce a list of facilities that charge a fee to members, and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, facility name, member cost, and monthly maintenance of the facilities in question.
```
SELECT facid, name, membercost, monthlymaintenance FROM cd.facilities
WHERE membercost > 0
AND membercost < ((1/50.0) * monthlymaintenance);
```
\
5.How can you produce a list of all facilities with the word 'Tennis' in their name?
```
SELECT * FROM cd.facilities
WHERE name LIKE '%Tennis%' ;
```
6.
```
SELECT * FROM cd.facilities
WHERE name LIKE '%Tennis%' ;
```
