**1. Find the number of students in each course.**
SELECT course\_id, COUNT(DISTINCT id) AS student\_count
FROM takes
GROUP BY course\_id;



**2. Find those departments where the average number of students are greater than 10.**

SELECT dept\_name
FROM student
GROUP BY dept\_name
HAVING COUNT(id) / COUNT(DISTINCT dept\_name) > 10;



**3. Find the total number of courses in each department.**

SELECT dept\_name, COUNT(course\_id) AS total\_courses
FROM course
GROUP BY dept\_name;



**4. Find the names and average salaries of all departments whose average salary is greater than 42000.**

SELECT dept\_name, AVG(salary) AS avg\_salary
FROM instructor
GROUP BY dept\_name
HAVING AVG(salary) > 42000;



**5. Find the enrolment of each section that was offered in Spring 2009SELECT course\_id, sec\_id, COUNT(id) AS enrollment**
FROM takes
WHERE semester = 'Spring'
AND year = 2009
GROUP BY course\_id, sec\_id;



**6. List all the courses with prerequisite courses, then display course id in increasing order.**

SELECT course\_id, prereq\_id
FROM prereq
ORDER BY course\_id ASC;



**7. Display the details of instructors sorting the salary in decreasing order.** 

SELECT \*
FROM instructor
ORDER BY salary DESC;



**8. Find the maximum total salary across the departments**

SELECT MAX(total\_salary) AS max\_total\_salary
FROM (
SELECT dept\_name, SUM(salary) AS total\_salary
FROM instructor
GROUP BY dept\_name);



**9. Find the average instructors’ salaries of those departments where the average salary is greater than 42000**
SELECT dept\_name, AVG(salary) AS avg\_salary
FROM instructor
GROUP BY dept\_name
HAVING AVG(salary) > 42000;



**10.  Find the sections that had the maximum enrolment in Spring 2010**
SELECT course\_id, sec\_id, COUNT(id) AS enrollment
FROM takes
WHERE semester = 'Spring'
AND year = 2010
GROUP BY course\_id, sec\_id
HAVING COUNT(id) = (
SELECT MAX(cnt)
FROM (
SELECT COUNT(id) AS cnt
FROM takes
WHERE semester = 'Spring'
AND year = 2010
GROUP BY course\_id, sec\_id));



**11. Find the names of all instructors who teach all students that belong to ‘CSE’ department**
SELECT i.name
FROM instructor i
WHERE NOT EXISTS (
SELECT s.id
FROM student s
WHERE s.dept\_name = 'Comp. Sci.'
AND NOT EXISTS (
SELECT 1
FROM takes t
JOIN teaches te
ON t.course\_id = te.course\_id
AND t.sec\_id = te.sec\_id
AND t.semester = te.semester
AND t.year = te.year
WHERE te.id = i.id
AND t.id = s.id));



**12. Find the average salary of those department where the average salary is greater than 50000 and total number of instructors in the department are more than 5.**
SELECT dept\_name,
AVG(salary) AS avg\_salary,
COUNT(id) AS instructor\_count
FROM instructor
GROUP BY dept\_name
HAVING AVG(salary) > 50000
AND COUNT(id) > 5;



**13. Find all departments with the maximum budget**
WITH max\_budget AS (
SELECT MAX(budget) AS max\_val
FROM department)
SELECT dept\_name, budget
FROM department
WHERE budget = (

&nbsp;	SELECT max\_val FROM max\_budget);



**14. Find all departments where the total salary is greater than the average of the total salary at all departments.**
WITH dept\_total AS (

SELECT dept\_name, SUM(salary) AS total\_salary
FROM instructor
GROUP BY dept\_name),
avg\_total AS (
SELECT AVG(total\_salary) AS avg\_salary
FROM dept\_total)
SELECT d.dept\_name, d.total\_salary
FROM dept\_total d
WHERE d.total\_salary > (SELECT avg\_salary FROM avg\_total);



**15. Transfer all the students from CSE department to IT department**

UPDATE student
SET dept\_name = 'IT'
WHERE dept\_name = 'Comp. Sci.';



**16. Increase salaries of instructors whose salary is over $100,000 by 3%, and all others receive a 5% raise**
UPDATE instructor
SET salary =
CASE
WHEN salary > 100000 THEN salary \* 1.03
ELSE salary \* 1.05
END;

