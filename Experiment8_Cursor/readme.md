# Experiment 8: PL/SQL Cursor Programs

## AIM
To write and execute PL/SQL programs using cursors and exception handling to manage runtime errors effectively and display appropriate messages.

## THEORY

In PL/SQL, cursors are used to handle query result sets row-by-row. 

There are two types of cursors:

- Implicit Cursors: Automatically created by PL/SQL for single-row queries.
- Explicit Cursors: Declared and controlled by the programmer for multi-row queries.

Types of Explicit Cursors:

1. Simple Cursor: Basic cursor to iterate over multiple rows.

2. Parameterized Cursor: Accepts parameters to filter the result dynamically.

3. Cursor FOR Loop: Simplifies cursor operations (open, fetch, close).

4. %ROWTYPE Cursor: Fetches entire row into a record using %ROWTYPE.

5. Cursor with FOR UPDATE: Used for row-level locking and updating the rows while looping.

**Syntax:**
```sql
DECLARE 
   <declarations section> 
BEGIN 
   <executable command(s)>
EXCEPTION 
   <exception handling> 
END;
```

### Basic Components of PL/SQL Block:

- DECLARE: Section to declare variables and constants.
- BEGIN: The execution section that contains PL/SQL statements.
- EXCEPTION: Handles errors or exceptions that occur in the program.
- END: Marks the end of the PL/SQL block.

**Exception Handling**

PL/SQL provides a robust mechanism to handle runtime errors using exception handling blocks. When an error occurs during execution, control is passed to the EXCEPTION section, where specific or general errors can be handled gracefully.

### Components of Exception Handling:
- Predefined Exceptions: Automatically raised by PL/SQL for common errors (e.g., NO_DATA_FOUND, TOO_MANY_ROWS, ZERO_DIVIDE).
- User-defined Exceptions: Declared explicitly in the declaration section using the EXCEPTION keyword.
- WHEN OTHERS: A generic handler for all exceptions not handled explicitly.

```sql
BEGIN
   -- Statements
EXCEPTION
   WHEN exception_name THEN
      -- Handling code
   WHEN OTHERS THEN
      -- Handling for unknown errors
END;
```

### **Question 1: Simple Cursor with Exception Handling**

**Write a PL/SQL program using a simple cursor to fetch employee names and designations from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: When no rows are fetched.
2. **OTHERS**: Any other unexpected errors during execution.

**Steps:**

- Create an `employees` table with fields `emp_id`, `emp_name`, and `designation`.
- Insert some sample data into the table.
- Use a simple cursor to fetch and display employee names and designations.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Output:**  
The program should display the employee details or an error message.

### CODE

```
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(100),
    designation VARCHAR2(100)
);

INSERT INTO employees VALUES (101, 'Alice Smith', 'Software Engineer');
INSERT INTO employees VALUES (102, 'Bob Jones', 'Database Administrator');
INSERT INTO employees VALUES (103, 'Carol White', 'Project Manager');
COMMIT;

DECLARE
    CURSOR c_emp IS
        SELECT emp_name, designation
        FROM employees;
        
    v_name employees.emp_name%TYPE;
    v_desig employees.designation%TYPE;
    v_count NUMBER := 0;
    
    e_no_data EXCEPTION;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_name, v_desig;
        EXIT WHEN c_emp%NOTFOUND;
        
        v_count := v_count + 1;
        DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_name || ' | Designation: ' || v_desig);
    END LOOP;
    CLOSE c_emp;
    
    IF v_count = 0 THEN
        RAISE e_no_data;
    END IF;

EXCEPTION
    WHEN e_no_data OR NO_DATA_FOUND THEN
        IF c_emp%ISOPEN THEN
            CLOSE c_emp;
        END IF;
        DBMS_OUTPUT.PUT_LINE('Error: No employee records found in the database.');
        
    WHEN OTHERS THEN
        IF c_emp%ISOPEN THEN
            CLOSE c_emp;
        END IF;
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
/
```

### OUTPUT
<img width="455" height="152" alt="image" src="https://github.com/user-attachments/assets/e4452810-6357-4bff-8110-e997120adb8a" />

---

### **Question 2: Parameterized Cursor with Exception Handling**

**Write a PL/SQL program using a parameterized cursor to retrieve and display employees with a salary in a given range. Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees meet the salary criteria.
2. **OTHERS**: For any unexpected errors during the execution.

**Steps:**

- Modify the `employees` table by adding a `salary` column.
- Insert sample salary values for the employees.
- Use a parameterized cursor to accept a salary range as input and fetch employees within that range.
- Implement exception handling to catch and display relevant error messages.

**Output:**  
The program should display the employee details within the specified salary range or an error message if no data is found.

### CODE

```
ALTER TABLE employees ADD salary NUMBER(10, 2);

UPDATE employees SET salary = 60000 WHERE emp_id = 101;
UPDATE employees SET salary = 85000 WHERE emp_id = 102;
UPDATE employees SET salary = 50000 WHERE emp_id = 103;
COMMIT;

DECLARE
    v_min_sal NUMBER := 55000;
    v_max_sal NUMBER := 90000;
    v_emp_name employees.emp_name%TYPE;
    v_designation employees.designation%TYPE;
    v_salary employees.salary%TYPE;
    v_rows_fetched NUMBER := 0;

    CURSOR c_emp_sal (p_min NUMBER, p_max NUMBER) IS
        SELECT emp_name, designation, salary
        FROM employees
        WHERE salary BETWEEN p_min AND p_max;
BEGIN
    OPEN c_emp_sal(v_min_sal, v_max_sal);
    LOOP
        FETCH c_emp_sal INTO v_emp_name, v_designation, v_salary;
        EXIT WHEN c_emp_sal%NOTFOUND;
        
        v_rows_fetched := v_rows_fetched + 1;
        DBMS_OUTPUT.PUT_LINE('Employee: ' || v_emp_name || ' | Designation: ' || v_designation || ' | Salary: ' || v_salary);
    END LOOP;
    CLOSE c_emp_sal;

    IF v_rows_fetched = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        IF c_emp_sal%ISOPEN THEN
            CLOSE c_emp_sal;
        END IF;
        DBMS_OUTPUT.PUT_LINE('Error: No employees found within the specified salary range.');
    WHEN OTHERS THEN
        IF c_emp_sal%ISOPEN THEN
            CLOSE c_emp_sal;
        END IF;
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
/
```

### OUTPUT

<img width="502" height="121" alt="image" src="https://github.com/user-attachments/assets/595e2938-c308-4504-9b39-eabc8cbd373e" />

---

### **Question 3: Cursor FOR Loop with Exception Handling**

**Write a PL/SQL program using a cursor FOR loop to retrieve and display all employee names and their department numbers from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no employees are found in the database.
2. **OTHERS**: For any other unexpected errors.

**Steps:**

- Modify the `employees` table by adding a `dept_no` column.
- Insert sample department numbers for employees.
- Use a cursor FOR loop to fetch and display employee names along with their department numbers.
- Implement exception handling to catch the relevant exceptions.

**Output:**  
The program should display employee names with their department numbers or the appropriate error message if no data is found.

### CODE

```
ALTER TABLE employees ADD dept_no NUMBER(5);

UPDATE employees SET dept_no = 10 WHERE emp_id = 101;
UPDATE employees SET dept_no = 20 WHERE emp_id = 102;
UPDATE employees SET dept_no = 10 WHERE emp_id = 103;
COMMIT;

DECLARE
    v_rows_fetched NUMBER := 0;

    CURSOR c_emp_dept IS
        SELECT emp_name, dept_no
        FROM employees;
BEGIN
    FOR emp_rec IN c_emp_dept LOOP
        v_rows_fetched := v_rows_fetched + 1;
        DBMS_OUTPUT.PUT_LINE('Employee: ' || emp_rec.emp_name || ' | Department: ' || emp_rec.dept_no);
    END LOOP;

    IF v_rows_fetched = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Error: No employees found in the database.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
/
```

### OUTPUT

<img width="360" height="103" alt="image" src="https://github.com/user-attachments/assets/34f15556-36cf-48ea-88c2-695cd1f9ac57" />

---

### **Question 4: Cursor with `%ROWTYPE` and Exception Handling**

**Write a PL/SQL program that uses a cursor with `%ROWTYPE` to fetch and display complete employee records (emp_id, emp_name, designation, salary). Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees are found in the database.
2. **OTHERS**: For any other errors that occur.

**Steps:**

- Modify the `employees` table by adding `emp_id`, `emp_name`, `designation`, and `salary` fields.
- Insert sample data into the `employees` table.
- Declare a cursor using `%ROWTYPE` to fetch complete rows from the `employees` table.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Output:**  
The program should display employee records or the appropriate error message if no data is found.

### CODE

```
ALTER TABLE employees ADD (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(50),
    designation VARCHAR2(50),
    salary NUMBER(10, 2)
);

INSERT INTO employees (emp_id, emp_name, designation, salary) VALUES (101, 'Alice Smith', 'Software Engineer', 60000);
INSERT INTO employees (emp_id, emp_name, designation, salary) VALUES (102, 'Bob Jones', 'Project Manager', 85000);
INSERT INTO employees (emp_id, emp_name, designation, salary) VALUES (103, 'Charlie Brown', 'Data Analyst', 50000);
COMMIT;

DECLARE
    CURSOR c_emp IS
        SELECT *
        FROM employees;
        
    v_emp_rec c_emp%ROWTYPE;
    v_rows_fetched NUMBER := 0;
BEGIN
    OPEN c_emp;
    LOOP
        FETCH c_emp INTO v_emp_rec;
        EXIT WHEN c_emp%NOTFOUND;
        
        v_rows_fetched := v_rows_fetched + 1;
        DBMS_OUTPUT.PUT_LINE('ID: ' || v_emp_rec.emp_id || 
                             ' | Name: ' || v_emp_rec.emp_name || 
                             ' | Designation: ' || v_emp_rec.designation || 
                             ' | Salary: ' || v_emp_rec.salary);
    END LOOP;
    CLOSE c_emp;

    IF v_rows_fetched = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        IF c_emp%ISOPEN THEN
            CLOSE c_emp;
        END IF;
        DBMS_OUTPUT.PUT_LINE('Error: No employees found in the database.');
    WHEN OTHERS THEN
        IF c_emp%ISOPEN THEN
            CLOSE c_emp;
        END IF;
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
/
```

### OUTPUT
<img width="538" height="107" alt="image" src="https://github.com/user-attachments/assets/58259bde-c044-45e5-8fd3-c23d1b71402d" />

---

### **Question 5: Cursor with FOR UPDATE Clause and Exception Handling**

**Write a PL/SQL program using a cursor with the `FOR UPDATE` clause to update the salary of employees in a specific department. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no rows are affected by the update.
2. **OTHERS**: For any unexpected errors during execution.

**Steps:**

- Modify the `employees` table to include a `dept_no` and `salary` field.
- Insert sample data into the `employees` table with different department numbers.
- Use a cursor with the `FOR UPDATE` clause to lock the rows of employees in a specific department and update their salary.
- Implement exception handling to handle `NO_DATA_FOUND` or other errors that may occur.

**Output:**  
The program should update employee salaries and display a message, or it should display an error message if no data is found.

### CODE

```
ALTER TABLE employees ADD (
    dept_no NUMBER(5),
    salary NUMBER(10, 2)
);

UPDATE employees SET dept_no = 10, salary = 60000 WHERE emp_id = 101;
UPDATE employees SET dept_no = 20, salary = 85000 WHERE emp_id = 102;
UPDATE employees SET dept_no = 10, salary = 50000 WHERE emp_id = 103;
COMMIT;

DECLARE
    v_dept_no employees.dept_no%TYPE := 10;
    v_raise_percent NUMBER := 0.10;
    v_rows_updated NUMBER := 0;

    CURSOR c_emp_update IS
        SELECT salary
        FROM employees
        WHERE dept_no = v_dept_no
        FOR UPDATE OF salary NOWAIT;
BEGIN
    FOR emp_rec IN c_emp_update LOOP
        UPDATE employees
        SET salary = salary + (salary * v_raise_percent)
        WHERE CURRENT OF c_emp_update;
        
        v_rows_updated := v_rows_updated + 1;
    END LOOP;

    IF v_rows_updated = 0 THEN
        RAISE NO_DATA_FOUND;
    ELSE
        COMMIT;
        DBMS_OUTPUT.PUT_LINE('Successfully updated salaries for ' || v_rows_updated || ' employee(s) in department ' || v_dept_no || '.');
    END IF;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error: No employees found in department ' || v_dept_no || ' to update.');
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
/
```

### OUTPUT
<img width="452" height="77" alt="image" src="https://github.com/user-attachments/assets/b70e9601-1624-49a3-8d61-331948fe28b3" />

---

## RESULT

Thus, the program successfully executed and displayed employee details using a cursor. 

