**1) Based on the University database Schema in Lab 2, write a row trigger that records along with the time any change made in the Takes (ID, course-id, sec-id, semester, year, grade) table in log\_change\_Takes (Time\_Of\_Change, ID, courseid, sec-id, semester, year, grade)**

CREATE OR REPLACE TRIGGER trg\_log\_takes

AFTER INSERT OR UPDATE OR DELETE

ON takes

FOR EACH ROW

BEGIN

&#x20;   IF INSERTING THEN

&#x20;       INSERT INTO log\_change\_takes

&#x20;       VALUES (

&#x20;           SYSTIMESTAMP,

&#x20;           :NEW.id,

&#x20;           :NEW.course\_id,

&#x20;           :NEW.sec\_id,

&#x20;           :NEW.semester,

&#x20;           :NEW.year,

&#x20;           :NEW.grade

&#x20;       );



&#x20;   ELSIF UPDATING THEN

&#x20;       INSERT INTO log\_change\_takes

&#x20;       VALUES (

&#x20;           SYSTIMESTAMP,

&#x20;           :NEW.id,

&#x20;           :NEW.course\_id,

&#x20;           :NEW.sec\_id,

&#x20;           :NEW.semester,

&#x20;           :NEW.year,

&#x20;           :NEW.grade

&#x20;       );



&#x20;   ELSIF DELETING THEN

&#x20;       INSERT INTO log\_change\_takes

&#x20;       VALUES (

&#x20;           SYSTIMESTAMP,

&#x20;           :OLD.id,

&#x20;           :OLD.course\_id,

&#x20;           :OLD.sec\_id,

&#x20;           :OLD.semester,

&#x20;           :OLD.year,

&#x20;           :OLD.grade

&#x20;       );

&#x20;   END IF;

END;

/



**2) Based on the University database schema in Lab: 2, write a row trigger to insert the existing values of the Instructor (ID, name, dept-name, salary) table into a new table Old\_ Data\_Instructor (ID, name, dept-name, salary) when the salary table is updated.**

CREATE OR REPLACE TRIGGER trg\_old\_instructor\_salary

BEFORE UPDATE OF salary

ON instructor

FOR EACH ROW

BEGIN

&#x20;   INSERT INTO old\_data\_instructor

&#x20;   VALUES (

&#x20;       :OLD.id,

&#x20;       :OLD.name,

&#x20;       :OLD.dept\_name,

&#x20;       :OLD.salary

&#x20;   );

END;

/



**3) Based on the University Schema, write a database trigger on Instructor that checks the following:**

** The name of the instructor is a valid name containing only alphabets.**

** The salary of an instructor is not zero and is positive**

** The salary does not exceed the budget of the department to which the instructor belongs**

CREATE OR REPLACE TRIGGER trg\_validate\_instructor

BEFORE INSERT OR UPDATE

ON instructor

FOR EACH ROW

DECLARE

&#x20;   v\_budget department.budget%TYPE;

BEGIN

&#x20;   -- Name must contain only alphabets

&#x20;   IF NOT REGEXP\_LIKE(:NEW.name, '^\[A-Za-z]+$') THEN

&#x20;       RAISE\_APPLICATION\_ERROR(

&#x20;           -20001,

&#x20;           'Instructor name must contain only alphabets'

&#x20;       );

&#x20;   END IF;



&#x20;   IF :NEW.salary <= 0 THEN

&#x20;       RAISE\_APPLICATION\_ERROR(

&#x20;           -20002,

&#x20;           'Salary must be positive and non-zero'

&#x20;       );

&#x20;   END IF;



&#x20;   SELECT budget

&#x20;   INTO v\_budget

&#x20;   FROM department

&#x20;   WHERE dept\_name = :NEW.dept\_name;



&#x20;   IF :NEW.salary > v\_budget THEN

&#x20;       RAISE\_APPLICATION\_ERROR(

&#x20;           -20003,

&#x20;           'Salary exceeds department budget'

&#x20;       );

&#x20;   END IF;



EXCEPTION

&#x20;   WHEN NO\_DATA\_FOUND THEN

&#x20;       RAISE\_APPLICATION\_ERROR(

&#x20;           -20004,

&#x20;           'Invalid department'

&#x20;       );

END;

/



**4) Create a transparent audit system for a table Client\_master (client\_no, name, address, Bal\_due). The system must keep track of the records that are being deleted or updated. The functionality being when a record is deleted or modified the original record details and the date of operation are stored in the auditclient (client\_no, name, bal\_due, operation, userid, opdate) table, then the delete or update is allowed to go through.**

CREATE OR REPLACE TRIGGER trg\_audit\_client

BEFORE UPDATE OR DELETE

ON client\_master

FOR EACH ROW

BEGIN

&#x20;   IF UPDATING THEN

&#x20;       INSERT INTO auditclient

&#x20;       VALUES (

&#x20;           :OLD.client\_no,

&#x20;           :OLD.name,

&#x20;           :OLD.bal\_due,

&#x20;           'UPDATE',

&#x20;           USER,

&#x20;           SYSDATE

&#x20;       );



&#x20;   ELSIF DELETING THEN

&#x20;       INSERT INTO auditclient

&#x20;       VALUES (

&#x20;           :OLD.client\_no,

&#x20;           :OLD.name,

&#x20;           :OLD.bal\_due,

&#x20;           'DELETE',

&#x20;           USER,

&#x20;           SYSDATE

&#x20;       );

&#x20;   END IF;

END;

/



**Additional Qs:**

**1) Write a row trigger to update the Bal\_Stock in item\_master when a new transaction is entered in the item\_transaction**

CREATE OR REPLACE TRIGGER trg\_update\_stock

AFTER INSERT

ON item\_transaction

FOR EACH ROW

BEGIN

&#x20;   IF :NEW.trans\_type = 'I' THEN

&#x20;       UPDATE item\_master

&#x20;       SET bal\_stock = bal\_stock + :NEW.qty

&#x20;       WHERE item\_no = :NEW.item\_no;



&#x20;   ELSIF :NEW.trans\_type = 'O' THEN

&#x20;       UPDATE item\_master

&#x20;       SET bal\_stock = bal\_stock - :NEW.qty

&#x20;       WHERE item\_no = :NEW.item\_no;

&#x20;   END IF;

END;

/

