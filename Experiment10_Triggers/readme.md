# Experiment 10: PL/SQL – Triggers

## AIM
To write and execute PL/SQL trigger programs for automating actions in response to specific table events like INSERT, UPDATE, or DELETE.

---

## THEORY

A **trigger** is a stored PL/SQL block that is automatically executed or fired when a specified event occurs on a table or view. Triggers can be used for enforcing business rules, auditing changes, or automatic updates.

### Types of Triggers:
- **Before Trigger**: Executes before the operation (INSERT, UPDATE, DELETE).
- **After Trigger**: Executes after the operation.
- **Row-level Trigger**: Executes for each affected row.
- **Statement-level Trigger**: Executes once for the triggering statement.

**Basic Syntax:**
```sql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE|AFTER INSERT|UPDATE|DELETE ON table_name
[FOR EACH ROW]
BEGIN
   -- trigger logic
END;
```

## 1. Write a trigger to log every insertion into a table.
**Steps:**
- Create two tables: `employees` (for storing data) and `employee_log` (for logging the inserts).
- Write an **AFTER INSERT** trigger on the `employees` table to log the new data into the `employee_log` table.

**Expected Output:**
- A new entry is added to the `employee_log` table each time a new record is inserted into the `employees` table.

### CODE
```
DROP TABLE employees CASCADE CONSTRAINTS;
DROP TABLE employee_log CASCADE CONSTRAINTS;

CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(50),
    designation VARCHAR2(50),
    salary NUMBER(10, 2)
);

CREATE TABLE employee_log (
    log_id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    emp_id NUMBER,
    emp_name VARCHAR2(50),
    action_type VARCHAR2(20),
    log_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE OR REPLACE TRIGGER trg_log_employee_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_log (emp_id, emp_name, action_type)
    VALUES (:NEW.emp_id, :NEW.emp_name, 'INSERT');
END;
/

INSERT INTO employees (emp_id, emp_name, designation, salary)
VALUES (101, 'Alice Smith', 'Software Engineer', 60000);

COMMIT;

SELECT * FROM employee_log;
```

### OUTPUT 

<img width="752" height="130" alt="image" src="https://github.com/user-attachments/assets/474bc8e2-5ca0-4227-8fd0-9039bf94fc04" />

---

## 2. Write a trigger to prevent deletion of records from a sensitive table.
**Steps:**
- Write a **BEFORE DELETE** trigger on the `sensitive_data` table.
- Use `RAISE_APPLICATION_ERROR` to prevent deletion and issue a custom error message.

**Expected Output:**
- If an attempt is made to delete a record from `sensitive_data`, an error message is raised, e.g., `ERROR: Deletion not allowed on this table.`

### CODE
```
CREATE TABLE sensitive_data (
    id NUMBER PRIMARY KEY,
    info VARCHAR2(100)
);

INSERT INTO sensitive_data VALUES (1, 'Confidential Financial Report');
COMMIT;

CREATE OR REPLACE TRIGGER trg_prevent_sensitive_delete
BEFORE DELETE ON sensitive_data
FOR EACH ROW
BEGIN
    RAISE_APPLICATION_ERROR(-20001, 'ERROR: Deletion not allowed on this table.');
END;
/

DELETE FROM sensitive_data WHERE id = 1;
```

### OUTPUT 


<img width="687" height="223" alt="image" src="https://github.com/user-attachments/assets/1b211708-72cb-4373-97f0-cd2500254cc8" />

---

## 3. Write a trigger to automatically update a `last_modified` timestamp.
**Steps:**
- Add a `last_modified` column to the `products` table.
- Write a **BEFORE UPDATE** trigger on the `products` table to set the `last_modified` column to the current timestamp whenever an update occurs.

**Expected Output:**
- The `last_modified` column in the `products` table is updated automatically to the current date and time when any record is updated.

### CODE
```
CREATE TABLE products (
    product_id NUMBER PRIMARY KEY,
    product_name VARCHAR2(50),
    price NUMBER(10, 2),
    last_modified TIMESTAMP
);

INSERT INTO products (product_id, product_name, price) 
VALUES (1, 'Laptop', 1200.00);
COMMIT;

CREATE OR REPLACE TRIGGER trg_update_last_modified
BEFORE UPDATE ON products
FOR EACH ROW
BEGIN
    :NEW.last_modified := SYSTIMESTAMP;
END;
/

UPDATE products 
SET price = 1100.00 
WHERE product_id = 1;
COMMIT;

SELECT product_id, product_name, price, last_modified 
FROM products;
```

### OUTPUT 

<img width="601" height="66" alt="image" src="https://github.com/user-attachments/assets/2248fc2b-face-432a-93b9-dc9ead596a15" />


---

## 4. Write a trigger to keep track of the number of updates made to a table.
**Steps:**
- Create an `audit_log` table with a counter column.
- Write an **AFTER UPDATE** trigger on the `customer_orders` table to increment the counter in the `audit_log` table every time a record is updated.

**Expected Output:**
- The `audit_log` table will maintain a count of how many updates have been made to the `customer_orders` table.

### CODE
```
CREATE TABLE customer_orders (
    order_id NUMBER PRIMARY KEY,
    customer_name VARCHAR2(50),
    order_status VARCHAR2(20)
);

CREATE TABLE audit_log (
    log_id NUMBER PRIMARY KEY,
    update_count NUMBER DEFAULT 0
);

INSERT INTO audit_log (log_id, update_count) VALUES (1, 0);
INSERT INTO customer_orders VALUES (101, 'John Doe', 'Pending');
COMMIT;

CREATE OR REPLACE TRIGGER trg_track_order_updates
AFTER UPDATE ON customer_orders
FOR EACH ROW
BEGIN
    UPDATE audit_log
    SET update_count = update_count + 1
    WHERE log_id = 1;
END;
/

UPDATE customer_orders 
SET order_status = 'Shipped' 
WHERE order_id = 101;
COMMIT;

SELECT * FROM audit_log;
```

### OUTPUT 

<img width="371" height="93" alt="image" src="https://github.com/user-attachments/assets/e8e614b1-0a6d-40c3-8309-e7f47e9251c7" />


---

## 5. Write a trigger that checks a condition before allowing insertion into a table.
**Steps:**
- Write a **BEFORE INSERT** trigger on the `employees` table to check if the inserted salary meets a specific condition (e.g., salary must be greater than 3000).
- If the condition is not met, raise an error to prevent the insert.

**Expected Output:**
- If the inserted salary in the `employees` table is below the condition (e.g., salary < 3000), the insert operation is blocked, and an error message is raised, such as: `ERROR: Salary below minimum threshold.`

### CODE
```
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(50),
    designation VARCHAR2(50),
    salary NUMBER(10, 2)
);

CREATE OR REPLACE TRIGGER trg_check_salary
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    IF :NEW.salary <= 3000 THEN
        RAISE_APPLICATION_ERROR(-20002, 'ERROR: Salary below minimum threshold.');
    END IF;
END;
/

INSERT INTO employees (emp_id, emp_name, designation, salary)
VALUES (101, 'John Doe', 'Intern', 2500);
```

### OUTPUT 

<img width="610" height="228" alt="image" src="https://github.com/user-attachments/assets/fe69cc19-ec15-4144-8769-044bb0094fcb" />


## RESULT
Thus, the PL/SQL trigger programs were written and executed successfully.
