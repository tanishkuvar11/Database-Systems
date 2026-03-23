**1) Write a procedure to display a message “Good Day to You”.**

CREATE OR REPLACE PROCEDURE good\_day\_proc

IS

BEGIN

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Good Day to You');

END;

/



BEGIN

&#x20;   good\_day\_proc;

END;

/



**2) Based on the University Database Schema in Lab 2, write a procedure which takes the dept\_name as input parameter and lists all the instructors associated with the department as well as list all the courses offered by the department. Also, write an anonymous block with the procedure call.**

CREATE OR REPLACE PROCEDURE dept\_details\_proc (

&#x20;   p\_dept\_name IN department.dept\_name%TYPE

)

IS

BEGIN

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Instructors in Department: ' || p\_dept\_name);



&#x20;   FOR i IN (

&#x20;       SELECT ID, name

&#x20;       FROM instructor

&#x20;       WHERE dept\_name = p\_dept\_name

&#x20;       ORDER BY ID

&#x20;   )

&#x20;   LOOP

&#x20;       DBMS\_OUTPUT.PUT\_LINE(i.ID || '  ' || i.name);

&#x20;   END LOOP;



&#x20;   DBMS\_OUTPUT.PUT\_LINE('Courses offered by Department: ' || p\_dept\_name);



&#x20;   FOR c IN (

&#x20;       SELECT course\_id, title

&#x20;       FROM course

&#x20;       WHERE dept\_name = p\_dept\_name

&#x20;       ORDER BY course\_id

&#x20;   )

&#x20;   LOOP

&#x20;       DBMS\_OUTPUT.PUT\_LINE(c.course\_id || '  ' || c.title);

&#x20;   END LOOP;

END;

/



DECLARE

&#x20;   v\_dept department.dept\_name%TYPE := '\&dept\_name';

BEGIN

&#x20;   dept\_details\_proc(v\_dept);

END;

/



**3) Based on the University Database Schema in Lab 2, write a Pl/Sql block of code that lists the most popular course (highest number of students take it) for each of the departments. It should make use of a procedure course\_popular which finds the most popular course in the given department.**

CREATE OR REPLACE PROCEDURE course\_popular (

&#x20;   p\_dept\_name IN department.dept\_name%TYPE

)

IS

&#x20;   v\_course\_id   course.course\_id%TYPE;

&#x20;   v\_title       course.title%TYPE;

&#x20;   v\_count       NUMBER;

BEGIN

&#x20;   SELECT course\_id, title, cnt

&#x20;   INTO v\_course\_id, v\_title, v\_count

&#x20;   FROM (

&#x20;       SELECT c.course\_id,

&#x20;              c.title,

&#x20;              COUNT(t.ID) AS cnt

&#x20;       FROM course c, takes t

&#x20;       WHERE c.course\_id = t.course\_id

&#x20;       AND c.dept\_name = p\_dept\_name

&#x20;       GROUP BY c.course\_id, c.title

&#x20;       ORDER BY cnt DESC

&#x20;   )

&#x20;   WHERE ROWNUM = 1;



&#x20;   DBMS\_OUTPUT.PUT\_LINE(

&#x20;       p\_dept\_name || '  ' ||

&#x20;       v\_course\_id || '  ' ||

&#x20;       v\_title || '  ' ||

&#x20;       v\_count

&#x20;   );



EXCEPTION

&#x20;   WHEN NO\_DATA\_FOUND THEN

&#x20;       DBMS\_OUTPUT.PUT\_LINE(

&#x20;           p\_dept\_name || '  No course enrollment'

&#x20;       );

END;

/



BEGIN

&#x20;   FOR d IN (SELECT dept\_name FROM department ORDER BY dept\_name)

&#x20;   LOOP

&#x20;       course\_popular(d.dept\_name);

&#x20;   END LOOP;

END;

/



**4) Based on the University Database Schema in Lab 2, write a procedure which takes the dept-name as input parameter and lists all the students associated with the department as well as list all the courses offered by the department. Also, write an anonymous block with the procedure call.**

CREATE OR REPLACE PROCEDURE dept\_student\_course\_proc (

&#x20;   p\_dept\_name IN department.dept\_name%TYPE

)

IS

BEGIN

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Students in Department: ' || p\_dept\_name);



&#x20;   FOR s IN (

&#x20;       SELECT ID, name

&#x20;       FROM student

&#x20;       WHERE dept\_name = p\_dept\_name

&#x20;       ORDER BY ID

&#x20;   )

&#x20;   LOOP

&#x20;       DBMS\_OUTPUT.PUT\_LINE(s.ID || '  ' || s.name);

&#x20;   END LOOP;



&#x20;   DBMS\_OUTPUT.PUT\_LINE('Courses offered by Department: ' || p\_dept\_name);



&#x20;   FOR c IN (

&#x20;       SELECT course\_id, title

&#x20;       FROM course

&#x20;       WHERE dept\_name = p\_dept\_name

&#x20;       ORDER BY course\_id

&#x20;   )

&#x20;   LOOP

&#x20;       DBMS\_OUTPUT.PUT\_LINE(c.course\_id || '  ' || c.title);

&#x20;   END LOOP;

END;

/



DECLARE

&#x20;   v\_dept department.dept\_name%TYPE := '\&dept\_name';

BEGIN

&#x20;   dept\_student\_course\_proc(v\_dept);

END;

/



**5) Write a function to return the Square of a given number and call it from an anonymous block.**

CREATE OR REPLACE FUNCTION square\_fn (

&#x20;   p\_num IN NUMBER

)

RETURN NUMBER

IS

BEGIN

&#x20;   RETURN p\_num \* p\_num;

END;

/



DECLARE

&#x20;   v\_num NUMBER := \&num;

&#x20;   v\_res NUMBER;

BEGIN

&#x20;   v\_res := square\_fn(v\_num);



&#x20;   DBMS\_OUTPUT.PUT\_LINE('Square = ' || v\_res);

END;

/



**6) Based on the University Database Schema in Lab 2, write a Pl/Sql block of code that lists the highest paid Instructor in each of the Department. It should make use of a function department\_highest which returns the highest paid Instructor for the given branch.**

CREATE OR REPLACE FUNCTION department\_highest (

&#x20;   p\_dept IN instructor.dept\_name%TYPE

)

RETURN VARCHAR

IS

&#x20;   v\_name   instructor.name%TYPE;

BEGIN

&#x20;   SELECT name

&#x20;   INTO v\_name

&#x20;   FROM instructor

&#x20;   WHERE dept\_name = p\_dept

&#x20;   AND salary = (

&#x20;       SELECT MAX(salary)

&#x20;       FROM instructor

&#x20;       WHERE dept\_name = p\_dept

&#x20;   );



&#x20;   RETURN v\_name;



EXCEPTION

&#x20;   WHEN NO\_DATA\_FOUND THEN

&#x20;       RETURN 'No Instructor';

END;

/



BEGIN

&#x20;   FOR d IN (SELECT dept\_name FROM department ORDER BY dept\_name)

&#x20;   LOOP

&#x20;       DBMS\_OUTPUT.PUT\_LINE(

&#x20;           d.dept\_name || '  ' ||

&#x20;           department\_highest(d.dept\_name)

&#x20;       );

&#x20;   END LOOP;

END;

/



**7) Based on the University Database Schema in Lab 2, create a package to include the following:**

**a) A named procedure to list the instructor\_names of given department**

**b) A function which returns the max salary for the given department**

**c) Write a PL/SQL block to demonstrate the usage of above package components**

CREATE OR REPLACE PACKAGE dept\_pkg AS



&#x20;   PROCEDURE list\_instructors (

&#x20;       p\_dept IN instructor.dept\_name%TYPE

&#x20;   );



&#x20;   FUNCTION max\_salary (

&#x20;       p\_dept IN instructor.dept\_name%TYPE

&#x20;   )

&#x20;   RETURN NUMBER;



END dept\_pkg;

/



CREATE OR REPLACE PACKAGE BODY dept\_pkg AS



&#x20;   PROCEDURE list\_instructors (

&#x20;       p\_dept IN instructor.dept\_name%TYPE

&#x20;   )

&#x20;   IS

&#x20;   BEGIN

&#x20;       FOR rec IN (

&#x20;           SELECT name

&#x20;           FROM instructor

&#x20;           WHERE dept\_name = p\_dept

&#x20;           ORDER BY name

&#x20;       )

&#x20;       LOOP

&#x20;           DBMS\_OUTPUT.PUT\_LINE(rec.name);

&#x20;       END LOOP;

&#x20;   END list\_instructors;





&#x20;   FUNCTION max\_salary (

&#x20;       p\_dept IN instructor.dept\_name%TYPE

&#x20;   )

&#x20;   RETURN NUMBER

&#x20;   IS

&#x20;       v\_sal NUMBER;

&#x20;   BEGIN

&#x20;       SELECT MAX(salary)

&#x20;       INTO v\_sal

&#x20;       FROM instructor

&#x20;       WHERE dept\_name = p\_dept;



&#x20;       RETURN v\_sal;

&#x20;   END max\_salary;



END dept\_pkg;

/



DECLARE

&#x20;   v\_dept instructor.dept\_name%TYPE := '\&dept\_name';

&#x20;   v\_max  NUMBER;

BEGIN

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Instructors in Department:');



&#x20;   dept\_pkg.list\_instructors(v\_dept);



&#x20;   v\_max := dept\_pkg.max\_salary(v\_dept);



&#x20;   DBMS\_OUTPUT.PUT\_LINE('Max Salary = ' || v\_max);

END;

/



**8) Write a PL/SQL procedure to return simple and compound interest (OUT parameters) along with the Total Sum (IN OUT) i.e. Sum of Principle and Interest taking as input the principle, rate of interest and number of years (IN parameters). Call this procedure from an anonymous block.**

CREATE OR REPLACE PROCEDURE interest\_proc (

&#x20;   p\_principal   IN     NUMBER,

&#x20;   p\_rate        IN     NUMBER,

&#x20;   p\_years       IN     NUMBER,

&#x20;   p\_simple      OUT    NUMBER,

&#x20;   p\_compound    OUT    NUMBER,

&#x20;   p\_total       IN OUT NUMBER

)

IS

BEGIN

&#x20;   p\_simple := (p\_principal \* p\_rate \* p\_years) / 100;



&#x20;   p\_compound :=

&#x20;       p\_principal \* POWER(1 + p\_rate / 100, p\_years)

&#x20;       - p\_principal;



&#x20;   p\_total := p\_principal + p\_simple;

END;

/



DECLARE

&#x20;   v\_p        NUMBER := \&principal;

&#x20;   v\_r        NUMBER := \&rate;

&#x20;   v\_y        NUMBER := \&years;



&#x20;   v\_si       NUMBER;

&#x20;   v\_ci       NUMBER;

&#x20;   v\_total    NUMBER;

BEGIN

&#x20;   interest\_proc(v\_p, v\_r, v\_y, v\_si, v\_ci, v\_total);



&#x20;   DBMS\_OUTPUT.PUT\_LINE('Simple Interest = ' || v\_si);

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Compound Interest = ' || v\_ci);

&#x20;   DBMS\_OUTPUT.PUT\_LINE('Total Amount = ' || v\_total);

END;

/

