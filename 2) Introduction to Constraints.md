**1. Create Employee table with following constraints:**

** Make EmpNo as Primary key.**

** Do not allow EmpName, Gender, Salary and Address to have null values.**

** Allow Gender to have one of the two values: ‘M’, ‘F’**

CREATE TABLE Employee(

EmpNo NUMBER(3) PRIMARY KEY,

EmpName VARCHAR(10) NOT NULL,

Gender CHAR(1) NOT NULL CHECK (Gender IN('M','F')) ,

Salary NUMBER(8) NOT NULL,

Address VARCHAR(20) NOT NULL,

DNo NUMBER(3));



**2. Create Department table with following:**

** Make DeptNo as Primary key**

** Make DeptName as candidate key**

CREATE TABLE Department(

DeptNo NUMBER(3) PRIMARY KEY,

DeptName VARCHAR(20) UNIQUE NOT NULL,

Location VARCHAR(20));



**3. Make DNo of Employee as foreign key which refers to DeptNo of Department.**

ALTER TABLE Employee 

ADD CONSTRAINT fk\_emp\_dept 

FOREIGN KEY(DNo) 

REFERENCES Department(DeptNo);



**4. Insert few tuples into Employee and Department which satisfies the above constraints.**

INSERT INTO Department VALUES (10, 'HR', 'Delhi');

INSERT INTO Department VALUES (20, 'SALES', 'Mumbai');

INSERT INTO Department VALUES (30, 'IT', 'Chennai');



INSERT INTO Employee VALUES (101, 'Ravi',  'M', 45000, 'Delhi', 10);

INSERT INTO Employee VALUES (102, 'Anita', 'F', 52000, 'Mumbai', 20);

INSERT INTO Employee VALUES (103, 'Kiran', 'M', 48000, 'Chennai', 30);



**5. Try to insert few tuples into Employee and Department which violates some of the**

**above constraints.**

INSERT INTO Department VALUES (10, 'FINANCE', 'Pune'); //Primary Key Violation

INSERT INTO Department VALUES (50, NULL, 'Delhi'); //Not Null Violation

INSERT INTO Employee VALUES (104, 'Sita', 'X', 40000, 'Jaipur', 10); //Gender Value Violation

INSERT INTO Employee VALUES (106, 'Amit', 'M', 42000, 'Bhopal', 99); //Foreign Key Violation



**6. Try to modify/delete a tuple which violates a constraint.**

UPDATE Employee

SET EmpNo = 101

WHERE EmpNo = 102; //Primary Key Violation



DELETE FROM Department

WHERE DeptNo = 10; //Foreign Key Violation



**7. Modify the foreign key constraint of Employee table such that whenever a department tuple is deleted, the employees belonging to that department will also be deleted**

ALTER TABLE Employee

DROP CONSTRAINT fk\_emp\_dept;



ALTER TABLE Employee

ADD CONSTRAINT fk\_emp\_dept

FOREIGN KEY(DNo)

REFERENCES Department(DeptNo)

ON DELETE CASCADE;



**8. Create a named constraint to set the default salary to 10000 and test the constraint by inserting a new record**

ALTER TABLE Employee

MODIFY Salary DEFAULT 10000;



INSERT INTO Employee (EmpNo, EmpName, Gender, Address, DNo) VALUES (108, 'Sunita', 'F', 'Bangalore', 20);



SELECT \* FROM Employee;



**9. List all Students with names and their department names.**

SELECT name, dept\_name FROM student;



**10. List all instructors in CSE department.**

SELECT name FROM instructor WHERE dept\_name='Comp. Sci.';



**11. Find the names of courses in CSE department which have 3 credits.**

SELECT course\_id, title FROM course WHERE dept\_name='Comp. Sci.' AND credits=3;



**12. For the student with ID 12345 (or any other value), show all course-id and title of all courses registered for by the student.**

SELECT takes.course\_id, course.title FROM takes

JOIN course ON takes.course\_id=course.course\_id

WHERE takes.ID = '12345';



**13. List all the instructors whose salary is in between 40000 and 90000.**

SELECT name, salary

FROM instructor

WHERE salary BETWEEN 40000 AND 90000;



**14. Display the IDs of all instructors who have never taught a course.**

SELECT ID

FROM instructor

MINUS

SELECT ID

FROM teaches;



**15. Find the student names, course names, and the year, for all students those who have attended classes in room-number 303.**

SELECT s.name, c.title, sec.year

FROM student s, takes t, section sec, course c

WHERE s.ID = t.ID

AND t.course\_id = sec.course\_id

AND t.sec\_id = sec.sec\_id

AND t.semester = sec.semester

AND t.year = sec.year

AND sec.course\_id = c.course\_id

AND sec.room\_number = 303;



**16. For all students who have opted courses in 2015, find their names and course id’s with the attribute course title replaced by c-name**

SELECT s.name, t.course\_id, c.title AS c\_name

FROM student s, takes t, course c

WHERE s.ID = t.ID

AND t.course\_id = c.course\_id

AND t.year = 2009;



**17. Find the names of all instructors whose salary is greater than the salary of at least one instructor of CSE department and salary replaced by inst-salary.**

SELECT name, salary AS inst\_salary

FROM instructor

WHERE salary > (

 	SELECT MIN(salary)

 	FROM instructor

 	WHERE dept\\\_name = 'CSE');



**18. Find the names of all instructors whose department name includes the substring ‘ch’**

SELECT name, dept\_name

FROM instructor

WHERE dept\_name LIKE '%ch%';



**19. List the student names along with the length of the student names.**

SELECT name, LENGTH(name) AS name\_length

FROM student;



**20. List the department names and 3 characters from 3rd position of each department name**

SELECT dept\_name, SUBSTR(dept\_name, 3, 3) AS sub

FROM department;



**21. List the instructor names in upper case**

SELECT UPPER(name) AS instructor\_name

FROM instructor;



**22. Replace NULL with value1(say 0) for a column in any of the table**

SELECT ID, course\_id, NVL(grade, 0) AS grade\_final

FROM takes;



**23. Display the salary and salary/3 rounded to nearest hundred from Instructor**

SELECT salary, ROUND(salary/3, -2) AS salary\_final

FROM instructor;



**24. Display the birth date of all the employees in the following format:**

** ‘DD-MON-YYYY’**

** ‘DD-MON-YY’**

** ‘DD-MM-YY’**

UPDATE Employee SET dob = DATE '2003-04-12' WHERE EmpNo = 1;

UPDATE Employee SET dob = DATE '2002-08-25' WHERE EmpNo = 2;

UPDATE Employee SET dob = DATE '2001-01-05' WHERE EmpNo = 3;

SELECT TO\_CHAR(dob,'DD-MON-YYYY') AS f1,

TO\_CHAR(dob,'DD-MON-YY') AS f2,

TO\_CHAR(dob,'DD-MM-YY') AS f3

FROM Employee;



**25. List the employee names and the year (fully spelled out) in which they are born**

** ‘YEAR’**

** ‘Year’**

** ‘year'**

SELECT EmpName,

TO\_CHAR(dob,'YEAR') AS Y1,

TO\_CHAR(dob,'Year') AS Y2,

TO\_CHAR(dob,'year') AS Y3

FROM Employee;
