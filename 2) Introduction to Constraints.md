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

