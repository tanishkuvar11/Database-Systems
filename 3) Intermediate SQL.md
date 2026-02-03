**UNION (Use union all to retain duplicates):**

**1. Find courses that ran in Fall 2009 or in Spring 2010**

SELECT course\_id FROM section

WHERE semester='Fall' AND year='2009'

UNION ALL

SELECT course\_id FROM section

WHERE semester='Spring' AND year='2010';



**INTERSECT (Use intersect all to retain duplicates):**

**2. Find courses that ran in Fall 2009 and in spring 2010**

SELECT course\_id FROM section

WHERE semester='Fall' AND year='2009'

INTERSECT

SELECT course\_id FROM section

WHERE semester='Spring' AND year='2010';



**MINUS:**

**3. Find courses that ran in Fall 2009 but not in Spring 2010**

SELECT course\_id FROM section

WHERE semester='Fall' AND year='2009'

MINUS

SELECT course\_id FROM section

WHERE semester='Spring' AND year='2010';



**Null values**

**4. Find the name of the course for which none of the students registered**

SELECT c.title

FROM course c

WHERE NOT EXISTS(

SELECT \*

FROM takes t

WHERE t.course\_id=c.course\_id);



**Nested Subqueries**

**Set Membership (in / not in):**

**5. Find courses offered in Fall 2009 and in Spring 2010**

SELECT s1.course\_id

FROM section s1

WHERE s1.semester = 'Fall' AND s1.year = 2009

AND s1.course\_id IN(

        SELECT s2.course\_id

        FROM section s2

        WHERE s2.course\_id = s1.course\_id

        AND s2.semester = 'Spring' AND s2.year = 2010);



**6. Find the total number of students who have taken course taught by the instructor with ID 10101.**

SELECT COUNT(DISTINCT t.ID)

FROM takes t

WHERE t.course\_id IN(

 	SELECT te.course\_id

 	FROM teaches te

 	WHERE te.id=10101);



**7. Find courses offered in Fall 2009 but not in Spring 2010.**

SELECT s1.course\_id

FROM section s1

WHERE semester='Fall' AND year='2009'

AND s1.course\_id NOT IN(

 	SELECT s2.course\_id

 	FROM section s2

 	WHERE s1.course\_id=s2.course\_id

 	AND s2.semester='Spring' AND s2.year=2010);



**8. Find the names of all students whose name is same as the instructor’s name.**

SELECT s.name

FROM student s

WHERE s.name IN(

 	SELECT i.name

 	FROM instructor i

 	WHERE s.name=i.name);



**9. Find names of instructors with salary greater than that of some (at least one) instructor in the Biology department.**

SELECT i1.name, i1.salary

FROM instructor i1

WHERE i1.salary > SOME(

 	SELECT i2.salary

 	FROM instructor i2

 	WHERE i2.dept\_name='Biology');



**10. Find the names of all instructors whose salary is greater than the salary of all instructors in the Biology department.**

SELECT i1.name, i1.salary

FROM instructor i1

WHERE i1.salary > ALL(

 	SELECT i2.salary

 	FROM instructor i2

 	WHERE i2.dept\_name='Biology');



**11. Find the departments that have the highest average salary.**

SELECT dept\_name, ROUND(AVG(salary), -2) as average

FROM instructor

GROUP BY dept\_name

ORDER BY ROUND(AVG(salary), -2) desc;



**12. Find the names of those departments whose budget is lesser than the average salary of all instructors.**

SELECT dept\_name, budget, (SELECT AVG(salary) FROM instructor) AS avg\_salary

FROM department

WHERE budget < ALL(

&nbsp;	SELECT AVG(salary)

&nbsp;	FROM instructor);



**13.  Find all courses taught in both the Fall 2009 semester and in the Spring 2010 semester.**

SELECT s1.course\_id

FROM section s1

WHERE s1.semester = 'Fall' AND s1.year = 2009

AND EXISTS(

 	SELECT s2.course\_id

 	FROM section s2

 	WHERE s2.course\_id = s1.course\_id

 	AND s2.semester = 'Spring' AND s2.year = 2010);



**14. Find all students who have taken all courses offered in the Biology department.**

SELECT \*

FROM student s

WHERE NOT EXISTS(

 	SELECT c.course\_id

 	FROM course c

 	WHERE c.dept\_name='Biology'

 	AND NOT EXISTS(

 		SELECT \*

 		FROM takes t

 		WHERE t.ID = s.ID

 		AND t.course\_id=c.course\_id)

 	);



**15. Find all courses that were offered at most once in 2009.**

SELECT course\_id

FROM section

WHERE year = 2009

GROUP BY course\_id

HAVING COUNT(\*) <= 1;



**16. Find all the students who have opted at least two courses offered by CSE department**

SELECT s.id, s.name

FROM student s

JOIN takes t ON s.id = t.id

JOIN course c ON t.course\_id = c.course\_id

WHERE c.dept\_name = 'Comp. Sci.'

GROUP BY s.id, s.name

HAVING COUNT(DISTINCT t.course\_id) >= 2;



**17. Find the average instructors salary of those departments where the average salary is greater than 42000**

SELECT dept\_name, avg\_salary

FROM (

 	SELECT dept\_name, AVG(salary) AS avg\_salary

 	FROM instructor

 	GROUP BY dept\_name)

WHERE avg\_salary > 42000;



**18. Create a view all\_courses consisting of course sections offered by Physics department in the Fall 2009, with the building and room number of each section.**

CREATE VIEW all\_courses AS

SELECT s.course\_id, s.sec\_id, s.building, s.room\_number

FROM section s

JOIN course c ON s.course\_id = c.course\_id

WHERE c.dept\_name = 'Physics'

AND s.semester = 'Fall'

AND s.year = 2009;





**19. Select all the courses from all\_courses view.**

SELECT \* FROM all\_courses;



**20. Create a view department\_total\_salary consisting of department name and total salary of that department.**

CREATE VIEW department\_total\_salary AS

SELECT dept\_name, SUM(salary) AS total\_salary

FROM instructor

GROUP BY dept\_name;


