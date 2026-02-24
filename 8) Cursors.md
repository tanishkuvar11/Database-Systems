**1) The HRD manager has decided to raise the salary of all the Instructors in a given department number by 5%. Whenever, any such raise is given to the instructor, a record for the same is maintained in the salary\_raise table. It includes the Instuctor Id, the date when the raise was given and the actual raise amount. Write a PL/SQL block to update the salary of each Instructor and insert a record in the salary\_raise table. salary\_raise(Instructor\_Id, Raise\_date, Raise\_amt)**

CREATE TABLE salary\_raise (

    Instructor\_Id  VARCHAR(5),

    Raise\_date     DATE,

    Raise\_amt      NUMERIC(8,2),

    FOREIGN KEY (Instructor\_Id) REFERENCES instructor(ID) ON DELETE CASCADE);



DECLARE

    dept  instructor.dept\_name%TYPE := '\&dept\_name';

BEGIN

    UPDATE instructor

    SET salary = salary \* 1.05

    WHERE dept\_name = dept;



    IF SQL%NOTFOUND THEN

        DBMS\_OUTPUT.PUT\_LINE('No instructors found in this department.');

    ELSE

        INSERT INTO salary\_raise (Instructor\_Id, Raise\_date, Raise\_amt)

        SELECT ID, SYSDATE, salary \* 0.05 / 1.05

        FROM instructor

        WHERE dept\_name = dept;



        DBMS\_OUTPUT.PUT\_LINE(SQL%ROWCOUNT || ' instructor(s) updated.');

    END IF;



    COMMIT;

END;

/



**2) Write a PL/SQL block that will display the ID, name, dept\_name and tot\_cred of the first 10 students with lowest total credit.**

BEGIN

    FOR rec IN (

        SELECT ID, name, dept\_name, tot\_cred

        FROM student

        ORDER BY tot\_cred ASC

    )

    LOOP

        EXIT WHEN SQL%ROWCOUNT = 10;



        DBMS\_OUTPUT.PUT\_LINE(

            rec.ID || '  ' ||

            rec.name || '  ' ||

            rec.dept\_name || '  ' ||

            rec.tot\_cred

        );

    END LOOP;

END;

/



**3) Print the Course details and the total number of students registered for each course along with the course details - (Course-id, title, dept-name, credits, instructor\_name, building, room-number, time-slot-id, tot\_student\_no )**

DECLARE

&nbsp;   CURSOR c\_course IS

&nbsp;       SELECT c.course\_id,

&nbsp;              c.title,

&nbsp;              c.dept\_name,

&nbsp;              c.credits,

&nbsp;              i.name AS instructor\_name,

&nbsp;              s.building,

&nbsp;              s.room\_number,

&nbsp;              s.time\_slot\_id,

&nbsp;              COUNT(t.ID) AS tot\_student\_no

&nbsp;       FROM course c, section s, teaches te, instructor i, takes t

&nbsp;       WHERE c.course\_id = s.course\_id

&nbsp;         AND s.course\_id = te.course\_id

&nbsp;         AND s.sec\_id = te.sec\_id

&nbsp;         AND s.semester = te.semester

&nbsp;         AND s.year = te.year

&nbsp;         AND te.ID = i.ID

&nbsp;         AND s.course\_id = t.course\_id

&nbsp;         AND s.sec\_id = t.sec\_id

&nbsp;         AND s.semester = t.semester

&nbsp;         AND s.year = t.year

&nbsp;       GROUP BY c.course\_id, c.title, c.dept\_name, c.credits,

&nbsp;                i.name, s.building, s.room\_number, s.time\_slot\_id;



BEGIN

&nbsp;   FOR rec IN c\_course LOOP

&nbsp;       DBMS\_OUTPUT.PUT\_LINE(

&nbsp;           rec.course\_id || '  ' ||

&nbsp;           rec.title || '  ' ||

&nbsp;           rec.dept\_name || '  ' ||

&nbsp;           rec.credits || '  ' ||

&nbsp;           rec.instructor\_name || '  ' ||

&nbsp;           rec.building || '  ' ||

&nbsp;           rec.room\_number || '  ' ||

&nbsp;           rec.time\_slot\_id || '  ' ||

&nbsp;           rec.tot\_student\_no

&nbsp;       );

&nbsp;   END LOOP;

END;

/



**4) Find all students who take the course with Course-id: CS101 and if he/ she has less than 30 total credit (tot-cred), deregister the student from that course. (Delete the entry in Takes table)**

DECLARE

&nbsp;   CURSOR c IS

&nbsp;       SELECT t.ID, s.name, s.tot\_cred

&nbsp;       FROM takes t, student s

&nbsp;       WHERE t.ID = s.ID

&nbsp;       AND t.course\_id = 'CS-101'

&nbsp;       FOR UPDATE;



BEGIN

&nbsp;   FOR rec IN c LOOP

&nbsp;       IF rec.tot\_cred < 30 THEN

&nbsp;           DELETE FROM takes

&nbsp;           WHERE CURRENT OF c;



&nbsp;           DBMS\_OUTPUT.PUT\_LINE(

&nbsp;               rec.name || ' deregistered from CS-101'

&nbsp;           );

&nbsp;       END IF;

&nbsp;   END LOOP;



&nbsp;   COMMIT;

END;

/

 

**5) Alter StudentTable(refer Lab No. 8 Exercise) by resetting column LetterGrade to F. Then write a PL/SQL block to update the table by mapping GPA to the corresponding letter grade for each student**

SET SERVEROUTPUT ON



DECLARE

&nbsp;   CURSOR c IS

&nbsp;       SELECT RollNo, GPA

&nbsp;       FROM StudentTable

&nbsp;       FOR UPDATE;



BEGIN

&nbsp;   FOR rec IN c LOOP



&nbsp;       IF rec.GPA >= 9 THEN

&nbsp;           UPDATE StudentTable

&nbsp;           SET LetterGrade = 'A+'

&nbsp;           WHERE CURRENT OF c;



&nbsp;       ELSIF rec.GPA >= 8 THEN

&nbsp;           UPDATE StudentTable

&nbsp;           SET LetterGrade = 'A'

&nbsp;           WHERE CURRENT OF c;



&nbsp;       ELSIF rec.GPA >= 7 THEN

&nbsp;           UPDATE StudentTable

&nbsp;           SET LetterGrade = 'B'

&nbsp;           WHERE CURRENT OF c;



&nbsp;       ELSIF rec.GPA >= 6 THEN

&nbsp;           UPDATE StudentTable

&nbsp;           SET LetterGrade = 'C'

&nbsp;           WHERE CURRENT OF c;



&nbsp;	ELSIF rec.GPA >= 5 THEN

            UPDATE StudentTable

            SET LetterGrade = 'D'

            WHERE CURRENT OF c;



&nbsp;	ELSIF rec.GPA >= 4 THEN

            UPDATE StudentTable

            SET LetterGrade = 'E'

            WHERE CURRENT OF c;





&nbsp;       ELSE

&nbsp;           UPDATE StudentTable

&nbsp;           SET LetterGrade = 'D'

&nbsp;           WHERE CURRENT OF c;

&nbsp;       END IF;



&nbsp;   END LOOP;



&nbsp;   COMMIT;

END;

/



**6) Write a PL/SQL block to print the list of Instructors teaching a specified course.**

DECLARE

&nbsp;   v\_course\_id  course.course\_id%TYPE := '\&course\_id';



&nbsp;   CURSOR c (p\_course\_id course.course\_id%TYPE) IS

&nbsp;       SELECT i.ID, i.name, i.dept\_name

&nbsp;       FROM instructor i, teaches t

&nbsp;       WHERE i.ID = t.ID

&nbsp;       AND t.course\_id = p\_course\_id;



BEGIN

&nbsp;   FOR rec IN c(v\_course\_id) LOOP

&nbsp;       DBMS\_OUTPUT.PUT\_LINE(

&nbsp;           rec.ID || '  ' ||

&nbsp;           rec.name || '  ' ||

&nbsp;           rec.dept\_name

&nbsp;       );

&nbsp;   END LOOP;

END;

/



**7) Write a PL/SQL block to list the students who have registered for a course taught by his/her advisor.**

DECLARE

&nbsp;   v\_advisor\_id advisor.i\_ID%TYPE := '\&advisor\_id';



&nbsp;   CURSOR c\_students (p\_advisor\_id advisor.i\_ID%TYPE) IS

&nbsp;       SELECT DISTINCT s.ID, s.name

&nbsp;       FROM student s,

&nbsp;            advisor a,

&nbsp;            teaches te,

&nbsp;            takes t

&nbsp;       WHERE s.ID = a.s\_ID

&nbsp;         AND a.i\_ID = p\_advisor\_id

&nbsp;         AND a.i\_ID = te.ID

&nbsp;         AND s.ID = t.ID

&nbsp;         AND t.course\_id = te.course\_id

&nbsp;         AND t.sec\_id = te.sec\_id

&nbsp;         AND t.semester = te.semester

&nbsp;         AND t.year = te.year;



BEGIN

&nbsp;   FOR rec IN c\_students(v\_advisor\_id) LOOP

&nbsp;       DBMS\_OUTPUT.PUT\_LINE(

&nbsp;           rec.ID || '  ' || rec.name

&nbsp;       );

&nbsp;   END LOOP;

END;

/



**Additional Qs:**

**1) Write a PL/SQL block that will display the name, department and salary of the top 10 highest paid instructors.**

DECLARE

 	CURSOR c IS

 	SELECT name, dept\_name, salary

 	FROM instructor

 	ORDER BY salary DESC;



 	v\_name   instructor.name%TYPE;

    	v\_dept   instructor.dept\_name%TYPE;

    	v\_salary instructor.salary%TYPE;



BEGIN

 	OPEN c;



 	LOOP

 		FETCH c INTO v\_name, v\_dept, v\_salary;



 		EXIT WHEN c%NOTFOUND OR c%ROWCOUNT>10;



 		DBMS\_OUTPUT.PUT\_LINE(v\_name || '  ' || v\_dept || '  ' || v\_salary);

 	END LOOP;



 	CLOSE c;

END;

/



**2) Repeat problem 1 using cursor for loops.**

DECLARE

&nbsp;	CURSOR c IS

&nbsp;	SELECT name, dept\_name, salary

&nbsp;	FROM instructor

&nbsp;	ORDER BY salary DESC;



&nbsp;	ctr NUMBER:=0;



BEGIN

&nbsp;	FOR rec IN c LOOP

&nbsp;		EXIT WHEN ctr>10;

&nbsp;		

&nbsp;		DBMS\_OUTPUT.PUT\_LINE(rec.name || '  ' || rec.dept\_name || '  ' || rec.salary);



&nbsp;		ctr:=ctr+1;

&nbsp;	END LOOP;

END;

/





**3) Write a PL/SQL block that would update the Bal\_stock in the item\_master(ItemId, Description, Bal\_stock) table each time a transaction takes place with an entry in the item\_transaction (TransID, ItemId, Quantity) table. The change in the item\_master table depends on the itemId. If the item is present in the item\_master table then Bal\_stock is updated. Otherwise, itemID is inserted into the item\_master table with ZERO as Bal\_stock and raises an exception.**

CREATE TABLE item\_master (

&nbsp;   ItemId      NUMBER PRIMARY KEY,

&nbsp;   Description VARCHAR2(50),

&nbsp;   Bal\_stock   NUMBER

);



CREATE TABLE item\_transaction (

&nbsp;   TransID  NUMBER PRIMARY KEY,

&nbsp;   ItemId   NUMBER,

&nbsp;   Quantity NUMBER

);



INSERT INTO item\_master VALUES (101, 'Pen', 50);

INSERT INTO item\_master VALUES (102, 'Notebook', 30);



INSERT INTO item\_transaction VALUES (1, 101, 10);

INSERT INTO item\_transaction VALUES (2, 102, -5);

INSERT INTO item\_transaction VALUES (3, 103, 20);



COMMIT;



DECLARE

&nbsp;   CURSOR c\_trans IS

&nbsp;       SELECT ItemId, Quantity

&nbsp;       FROM item\_transaction;



&nbsp;   CURSOR c\_item (p\_itemid item\_master.ItemId%TYPE) IS

&nbsp;       SELECT Bal\_stock

&nbsp;       FROM item\_master

&nbsp;       WHERE ItemId = p\_itemid

&nbsp;       FOR UPDATE;



&nbsp;   item\_not\_found EXCEPTION;



&nbsp;   v\_stock item\_master.Bal\_stock%TYPE;



BEGIN

&nbsp;   FOR rec IN c\_trans LOOP



&nbsp;       OPEN c\_item(rec.ItemId);

&nbsp;       FETCH c\_item INTO v\_stock;



&nbsp;       IF c\_item%FOUND THEN

&nbsp;           UPDATE item\_master

&nbsp;           SET Bal\_stock = v\_stock - rec.Quantity

&nbsp;           WHERE CURRENT OF c\_item;



&nbsp;           CLOSE c\_item;



&nbsp;       ELSE

&nbsp;           CLOSE c\_item;



&nbsp;           INSERT INTO item\_master(ItemId, Description, Bal\_stock)

&nbsp;           VALUES (rec.ItemId, 'NEW ITEM', 0);



&nbsp;           RAISE item\_not\_found;

&nbsp;       END IF;



&nbsp;   END LOOP;



&nbsp;   COMMIT;



EXCEPTION

&nbsp;   WHEN item\_not\_found THEN

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Item not found. Inserted with ZERO stock.');

END;

/



**4) Write a PL/SQL block to find the number of courses offered and number of students of each department. Use a parameterized cursor which takes department name as a parameter and calculates the number of courses offered and number of students of that department.**

DECLARE

&nbsp;   v\_dept department.dept\_name%TYPE := '\&dept\_name';



&nbsp;   CURSOR c\_dept (p\_dept department.dept\_name%TYPE) IS

&nbsp;       SELECT COUNT(DISTINCT c.course\_id),

&nbsp;              COUNT(DISTINCT s.ID)

&nbsp;       FROM course c, student s

&nbsp;       WHERE c.dept\_name = p\_dept

&nbsp;         AND s.dept\_name = p\_dept;



&nbsp;   v\_courses  NUMBER;

&nbsp;   v\_students NUMBER;



BEGIN

&nbsp;   OPEN c\_dept(v\_dept);

&nbsp;   FETCH c\_dept INTO v\_courses, v\_students;



&nbsp;   IF c\_dept%FOUND THEN

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Courses: ' || v\_courses);

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Students: ' || v\_students);

&nbsp;   ELSE

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Department not found.');

&nbsp;   END IF;



&nbsp;   CLOSE c\_dept;

END;

/



**5) Write a PL/SQL block that will insert a new record in Takes (ID, course-id, sec-id, semester, year, grade) table. Check the total number of students registered for the course and if it exceeds 30, then undo the insert made to the Takes table.**

SET SERVEROUTPUT ON;



DECLARE

&nbsp;   v\_id        takes.ID%TYPE        := '\&ID';

&nbsp;   v\_course    takes.course\_id%TYPE := '\&course\_id';

&nbsp;   v\_sec       takes.sec\_id%TYPE    := '\&sec\_id';

&nbsp;   v\_sem       takes.semester%TYPE  := '\&semester';

&nbsp;   v\_year      takes.year%TYPE      := \&year;

&nbsp;   v\_grade     takes.grade%TYPE     := '\&grade';



&nbsp;   v\_count NUMBER;



BEGIN

&nbsp;   INSERT INTO takes(ID, course\_id, sec\_id, semester, year, grade)

&nbsp;   VALUES (v\_id, v\_course, v\_sec, v\_sem, v\_year, v\_grade);



&nbsp;   SELECT COUNT(\*)

&nbsp;   INTO v\_count

&nbsp;   FROM takes

&nbsp;   WHERE course\_id = v\_course

&nbsp;     AND sec\_id    = v\_sec

&nbsp;     AND semester  = v\_sem

&nbsp;     AND year      = v\_year;



&nbsp;   IF v\_count > 30 THEN

&nbsp;       ROLLBACK;

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Limit exceeded. Insert undone.');

&nbsp;   ELSE

&nbsp;       COMMIT;

&nbsp;       DBMS\_OUTPUT.PUT\_LINE('Student registered successfully.');

&nbsp;   END IF;



END;

/

