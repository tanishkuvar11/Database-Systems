**1. Write a PL/SQL block to display the GPA of given student.**

DECLARE

 	R NUMBER(2);

 	G NUMBER(2,1);

BEGIN

 	R:='\&R';



 	SELECT GPA INTO G

 	FROM StudentTable

 	WHERE RollNo=R;



 	DBMS\_OUTPUT.PUT\_LINE('GPA: ' || TO\_CHAR(G));

END;

/



**2. Write a PL/SQL block to display the letter grade(0-4: F; 4-5: E; 5-6: D; 6-7: C; 7-8: B; 8-9: A; 9-10: A+} of given student**

DECLARE

 	R NUMBER(2);

 	G NUMBER(2,1);

 	Grade VARCHAR(1);

BEGIN

 	R:='\&R';



 	SELECT GPA INTO G

 	FROM StudentTable

 	WHERE RollNo=R;



 	IF G>=9 AND G<=10 THEN

 		Grade:='A+';

 	ELSIF G>=8 AND G<9 THEN

 		Grade:='A';

 	ELSIF G>=7 AND G<8 THEN

 		Grade:='B';

 	ELSIF G>=6 AND G<7 THEN

 		Grade:='C';

 	ELSIF G>=5 AND G<6 THEN

 		Grade:='D';

 	ELSIF G>=4 AND G<5 THEN

 		Grade:='E';

 	ELSE

 		Grade:='F';

 	END IF;



 	DBMS\_OUTPUT.PUT\_LINE('Grade: ' || Grade);

END;

/



**3. Input the date of issue and date of return for a book. Calculate and display the fine with the appropriate message using a PL/SQL block.**

DECLARE

    issue\_date   DATE;

    return\_date  DATE;

    late\_days    NUMBER;

    fine         NUMBER := 0;

BEGIN

    issue\_date:=TO\_DATE('\&issue\_date', 'DD-MM-YYYY');

    return\_date:=TO\_DATE('\&return\_date', 'DD-MM-YYYY');



    late\_days:=return\_date-issue\_date;



    IF late\_days<=7 THEN

        fine:=0;

    ELSIF late\_days BETWEEN 8 AND 15 THEN

    	fine:=late\_days\*1;

    ELSIF late\_days BETWEEN 16 AND 30 THEN

        fine:=late\_days\*2;

    ELSE

        fine:=late\_days\*5;

    END IF;



    DBMS\_OUTPUT.PUT\_LINE('Late days: ' || late\_days);

    DBMS\_OUTPUT.PUT\_LINE('Fine: Rs. ' || fine);

END;

/



4\.

DECLARE

    Grade VARCHAR2(2);

BEGIN

    FOR rec IN (SELECT RollNo, GPA FROM StudentTable WHERE RollNo BETWEEN 1 AND 5) LOOP



        IF rec.GPA>=9 THEN

            Grade:='A+';

        ELSIF rec.GPA>=8 THEN

            Grade:='A';

        ELSIF rec.GPA>=7 THEN

            Grade:='B';

        ELSIF rec.GPA>=6 THEN

            Grade:='C';

        ELSIF rec.GPA>=5 THEN

            Grade:='D';

        ELSIF rec.GPA>=4 THEN

            Grade:='E';

        ELSE

            Grade:='F';

        END IF;



        DBMS\_OUTPUT.PUT\_LINE('RollNo: ' || rec.RollNo ||' GPA: ' || rec.GPA ||' Grade: ' || Grade);



    END LOOP;

END;

/



**5. Alter StudentTable by appending an additional column LetterGrade Varchar2(2). Then write a PL/SQL block to update the table with letter grade of each student.**

ALTER TABLE StudentTable

ADD LetterGrade VARCHAR2(2);



DECLARE

    v\_grade VARCHAR2(2);

BEGIN



    FOR rec IN(SELECT RollNo, GPA FROM StudentTable) LOOP

        IF rec.GPA>=9 THEN

            v\_grade:='A+';

        ELSIF rec.GPA>=8 THEN

            v\_grade:='A';

        ELSIF rec.GPA>=7 THEN

            v\_grade:='B';

        ELSIF rec.GPA>=6 THEN

            v\_grade:='C';

        ELSIF rec.GPA>=5 THEN

            v\_grade:='D';

        ELSIF rec.GPA>=4 THEN

            v\_grade:='E';

        ELSE

            v\_grade:='F';

        END IF;



        UPDATE StudentTable

        SET LetterGrade=v\_grade

        WHERE RollNo=rec.RollNo;



    END LOOP;

    COMMIT;

    DBMS\_OUTPUT.PUT\_LINE('Letter grades updated successfully.');

END;

/



**6. Write a PL/SQL block to find the student with max. GPA without using aggregate Function.**

DECLARE

    v\_max\_gpa StudentTable.GPA%TYPE:=-1;

    v\_rollno StudentTable.RollNo%TYPE;

BEGIN

    FOR rec IN(SELECT RollNo, GPA FROM StudentTable) LOOP

        IF rec.GPA>v\_max\_gpa THEN

            v\_max\_gpa := rec.GPA;

            v\_rollno  := rec.RollNo;

        END IF;

    END LOOP;



    DBMS\_OUTPUT.PUT\_LINE('Student with Maximum GPA:');

    DBMS\_OUTPUT.PUT\_LINE('RollNo: ' || v\_rollno || ' GPA: ' || v\_max\_gpa);

END;

/



**7. Implement lab exercise 4 using GOTO.**

DECLARE

    v\_rollno NUMBER:=1;

    v\_gpa StudentTable.GPA%TYPE;

    v\_grade VARCHAR2(2);

BEGIN



<<start\_loop>>



    IF v\_rollno>5 THEN

        GOTO end\_loop;

    END IF;



    SELECT GPA

    INTO v\_gpa

    FROM StudentTable

    WHERE RollNo=v\_rollno;



    IF v\_gpa>=9 THEN

        v\_grade:='A+';

    ELSIF v\_gpa>=8 THEN

        v\_grade:='A';

    ELSIF v\_gpa>=7 THEN

        v\_grade:='B';

    ELSIF v\_gpa>=6 THEN

        v\_grade:='C';

    ELSIF v\_gpa>=5 THEN

        v\_grade:='D';

    ELSIF v\_gpa>=4 THEN

        v\_grade:='E';

    ELSE

        v\_grade:='F';

    END IF;



    DBMS\_OUTPUT.PUT\_LINE('RollNo: ' || v\_rollno || ' GPA: ' || v\_gpa || ' Grade: ' || v\_grade);



    v\_rollno := v\_rollno + 1;



    GOTO start\_loop;



<<end\_loop>>

NULL;



END;

/



**8. Based on the University database schema, write a PL/SQL block to display the details of the Instructor whose name is supplied by the user. Use exceptions to show appropriate error message for the following cases:**

**a. Multiple instructors with the same name**

**b. No instructor for the given name**

DECLARE

    v\_name instructor.name%TYPE := '\&name';

    v\_id instructor.ID%TYPE;

    v\_dept instructor.dept\_name%TYPE;

    v\_salary instructor.salary%TYPE;

BEGIN



    SELECT ID, dept\_name, salary

    INTO v\_id, v\_dept, v\_salary

    FROM instructor

    WHERE name=v\_name;



    DBMS\_OUTPUT.PUT\_LINE('Instructor Details:');

    DBMS\_OUTPUT.PUT\_LINE('ID: ' || v\_id);

    DBMS\_OUTPUT.PUT\_LINE('Name: ' || v\_name);

    DBMS\_OUTPUT.PUT\_LINE('Dept: ' || v\_dept);

    DBMS\_OUTPUT.PUT\_LINE('Salary: ' || v\_salary);



EXCEPTION

    WHEN NO\_DATA\_FOUND THEN

        DBMS\_OUTPUT.PUT\_LINE('No instructor found with name: ' || v\_name);



    WHEN TOO\_MANY\_ROWS THEN

        DBMS\_OUTPUT.PUT\_LINE('Multiple instructors found with name: ' || v\_name);



    WHEN OTHERS THEN

        DBMS\_OUTPUT.PUT\_LINE('Unexpected error occurred.');



END;

/



**Additional :**



1. **Write a PL/SQL block to find out if a year is a leap year**

DECLARE

 	d NUMBER(4);



BEGIN

 	d:='\&D';



 	IF (MOD(d,4)=0 AND MOD(d,100)!=0) OR MOD(d,400)=0 THEN

 		DBMS\_OUTPUT.PUT\_LINE(d || ' is a leap year');

 	ELSE

 		DBMS\_OUTPUT.PUT\_LINE(d || ' is not a leap year');

 	END IF;

END;

/



**2. You went to a video store and rented DVD that is due in 3 days from the rental date. Input the rental date, rental month and rental year. Calculate and print the return date, return month, and return year.**

DECLARE

&nbsp;	d DATE;

&nbsp;	r DATE;



BEGIN

&nbsp;	d:=TO\_DATE('\&d', 'DD-MM-YYYY');

&nbsp;	r:=d+3;

&nbsp;	DBMS\_OUTPUT.PUT\_LINE('Return date: '||r);

END;

/

&nbsp;	



**3. Write a simple loop such that message is displayed when a loop exceeds a particular value.**

DECLARE

&nbsp;   v\_num NUMBER:=1;

BEGIN

&nbsp;	WHILE v\_num<10 LOOP

&nbsp;       IF v\_num>5 THEN

&nbsp;           DBMS\_OUTPUT.PUT\_LINE('Value exceeded 5 at: ' || v\_num);

&nbsp;       END IF;



&nbsp;       v\_num:=v\_num + 1;

&nbsp;   END LOOP;

END;

/



**4. Write a PL/SQL block to print all odd numbers between 1 and 10**

DECLARE

&nbsp;	v\_num NUMBER:=1;

BEGIN

&nbsp;	WHILE v\_num<10 LOOP

&nbsp;		IF MOD(v\_num, 2)=1 THEN

&nbsp;			DBMS\_OUTPUT.PUT\_LINE(v\_num);

&nbsp;		END IF;

&nbsp;	v\_num:=v\_num + 1;

&nbsp;	END LOOP;

END;

**/**



