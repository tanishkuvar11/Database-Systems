**1. Retrieve the birth date and address of the employee(s) whose name is ‘John B. Smith’. Retrieve the name and address of all employees who work for the ‘Research’ department.**

SELECT Bdate, Address 

FROM Employee 

WHERE Fname='John' AND Minit='B' AND Lname='Smith';



SELECT E.Fname, E.Lname, E.Address

FROM Employee E

JOIN Department D ON E.Dno = D.Dnumber

WHERE D.Dname='Research';



**2. For every project located in ‘Stanford’, list the project number, the controlling department number, and the department manager’s last name, address, and birth date.**

SELECT P.Pnumber, P.Dnum, E.Lname, E.Address, E.Bdate

FROM Project P

JOIN Department D ON P.Dnum = D.Dnumber

JOIN Employee E ON D.Mgr\_ssn = E.Ssn

WHERE P.Plocation='Stanford';



**3. For each employee, retrieve the employee’s first and last name and the first and last name of his or her immediate supervisor.**

SELECT E.Fname AS Employee\_Fname, E.Lname AS Employee\_Lname, S.Fname AS Supervisor\_Fname, S.Lname AS Supervisor\_Lname

FROM Employee E

LEFT JOIN Employee S ON E.Super\_ssn = S.Ssn;



**4. Make a list of all project numbers for projects that involve an employee whose last name is ‘Smith’, either as a worker or as a manager of the department that controls the project.**

SELECT DISTINCT P.Pnumber

FROM Project P

JOIN Works\_On W ON P.Pnumber = W.Pno

JOIN Employee E ON W.Essn = E.Ssn

WHERE E.Lname='Smith'

UNION

SELECT DISTINCT P.Pnumber

FROM Project P

JOIN Department D ON P.Dnum = D.Dnumber

JOIN Employee E ON D.Mgr\_ssn = E.Ssn

WHERE E.Lname='Smith';



**5. Show the resulting salaries if every employee working on the ‘ProductX’ project is given a 10 percent raise.**

SELECT E.Fname, E.Lname, E.Salary \* 1.1 AS New\_Salary

FROM Employee E

JOIN Works\_On W ON E.Ssn = W.Essn

JOIN Project P ON W.Pno = P.Pnumber

WHERE P.Pname='ProductX';



**6. Retrieve a list of employees and the projects they are working on, ordered by department and, within each department, ordered alphabetically by last name, then first name.**

SELECT D.Dname, E.Fname, E.Lname, P.Pname

FROM Employee E

JOIN Department D ON E.Dno = D.Dnumber

JOIN Works\_On W ON E.Ssn = W.Essn

JOIN Project P ON W.Pno = P.Pnumber

ORDER BY D.Dname, E.Lname, E.Fname;



**7. Retrieve the name of each employee who has a dependent with the same first name and is the same sex as the employee.**

SELECT DISTINCT E.Fname, E.Lname

FROM Employee E

JOIN Dependent D ON E.Ssn = D.Essn

WHERE E.Fname = D.Dependent\_name AND E.Sex = D.Sex;



**8. Retrieve the names of employees who have no dependents.**

SELECT E.Fname, E.Lname

FROM Employee E

LEFT JOIN Dependent D ON E.Ssn = D.Essn

WHERE D.Essn IS NULL;



**9. List the names of managers who have at least one dependent.**

SELECT DISTINCT E.Fname, E.Lname

FROM Employee E

JOIN Department D ON E.Ssn = D.Mgr\_ssn

JOIN Dependent Dep ON E.Ssn = Dep.Essn;



**10. Find the sum of the salaries of all employees, the maximum salary, the minimum salary, and the average salary.**

SELECT SUM(Salary) AS Total\_Salary, MAX(Salary) AS Max\_Salary, MIN(Salary) AS Min\_Salary, AVG(Salary) AS Avg\_Salary

FROM Employee;



**11. For each project, retrieve the project number, the project name, and the number of employees who work on that project.**

SELECT P.Pnumber, P.Pname, COUNT(W.Essn) AS Num\_Employees

FROM Project P

LEFT JOIN Works\_On W ON P.Pnumber = W.Pno

GROUP BY P.Pnumber, P.Pname;



**12. For each project on which more than two employees work, retrieve the project number, the project name, and the number of employees who work on the project.**

SELECT P.Pnumber, P.Pname, COUNT(W.Essn) AS Num\_Employees

FROM Project P

JOIN Works\_On W ON P.Pnumber = W.Pno

GROUP BY P.Pnumber, P.Pname

HAVING COUNT(W.Essn) > 2;



**13. For each department that has more than five employees, retrieve the department number and the number of its employees who are making more than 40,000.**

SELECT D.Dnumber, COUNT(E.Ssn) AS Num\_High\_Salary\_Employees

FROM Department D

JOIN Employee E ON D.Dnumber = E.Dno

WHERE E.Salary > 40000

GROUP BY D.Dnumber

HAVING COUNT(E.Ssn) > 5;



