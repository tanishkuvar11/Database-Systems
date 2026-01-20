**1. Create a table employee with (emp\_no, emp\_name, emp\_address)**

CREATE TABLE employee(

emp\_no NUMBER(3),

emp\_name VARCHAR(20),

emp\_address VARCHAR(30));



**2. Insert five employees information.**

INSERT INTO employee VALUES(101, 'Arun', 'Manipal');

INSERT INTO employee VALUES(102, 'Suresh', 'Udupi');

INSERT INTO employee VALUES(103, 'Raju', 'Mangalore');

INSERT INTO employee VALUES(104, 'Pooja', 'Mangalore');

INSERT INTO employee VALUES(105, 'Nisha', 'Manipal');



**3. Display names of all employees.**

SELECT emp\_name FROM employee;



**4. Display all the employees from 'MANIPAL'.**

SELECT \* FROM employee WHERE emp\_address='Manipal';



**5. Add a column named salary to employee table.**

ALTER TABLE employee ADD salary NUMBER(7);



**6. Assign the salary for all employees.**

UPDATE employee SET salary=60000;



**7. View the structure of the table employee using describe.**

DESCRIBE employee;



**8. Delete all the employees from 'MANGALORE'**

DELETE FROM employee WHERE emp\_address='Mangalore';



**9. Rename employee as employee1.**

RENAME employee TO employee1;



**10. Drop the table employee1.**

DROP TABLE employee1;

