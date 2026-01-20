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

