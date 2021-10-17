All the questions for this section can be found [here.](https://pgexercises.com/questions/recursive/)

\
1.Find the upward recommendation chain for member ID 27: that is, the member who recommended them, and the member who recommended that member, and so on. Return member ID, first name, and surname. Order by descending member id.


```
WITH RECURSIVE myrec(reco) AS 
	(
  SELECT 27
  UNION ALL
  SELECT mem.recommendedby
  FROM cd.members AS mem, myrec AS mr
  WHERE mr.reco IS NOT NULL AND mem.memid = mr.reco
	)
SELECT mem.memid AS recommender, mem.firstname, mem.surname
FROM cd.members AS mem
JOIN myrec AS mr
ON mem.memid = mr.reco 
WHERE mem.memid != 27
;

OR a more readable Solution

WITH RECURSIVE myrecommender(recommender) AS
	(SELECT recommendedby FROM cd.members 
	 WHERE memid = 27
	 UNION 
	 SELECT mem.recommendedby FROM myrecommender AS myrec
	 	JOIN cd.members AS mem 
	 		ON mem.memid = myrec.recommender
	 )
	 	 
SELECT myrec.recommender, mem.firstname, mem.surname 
FROM myrecommender AS myrec
JOIN cd.members AS mem
ON mem.memid = myrec.recommender
;
```
