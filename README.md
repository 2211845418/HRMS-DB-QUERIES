# HRMS-DB-QUERIES

## Employee Management

### **1. Retrieve All Employees with Department Information**

**Purpose:**

Fetches a list of all employees, including their department names, phone numbers, and salaries. This query provides a comprehensive view of employee data.

**Query Code:**

```sql
SELECT
    e.EmployeeID,
    e.FullName,
    d.DepartmentName,
    e.PhoneNumber,
    e.Salary
FROM
    employee e
LEFT JOIN
    department d ON e.DepartmentID = d.DepartmentID;
```

---

### **2. Get Employee IDs and Names**

**Purpose:**

Retrieves the Employee IDs and full names of all employees. This query is useful for generating employee selection lists.

**Query Code:**

```sql
SELECT
    EmployeeID,
    FullName
FROM
    employee;
```

---

### **3. Retrieve Full Employee Information by ID**

**Purpose:**

Fetches detailed information about a specific employee, including their personal details, department, start date, salary, and login credentials. This query ensures comprehensive access to employee records.

**Query Code:**

```sql
SELECT
    e.FullName,
    e.PhoneNumber,
    d.DepartmentName,
    e.StartDate,
    e.Salary,
    u.Email,
    u.Password
FROM
    employee e
LEFT JOIN
    department d ON e.DepartmentID = d.DepartmentID
INNER JOIN
    user u ON u.EmployeeID = e.EmployeeID
WHERE
    e.EmployeeID = :employee_id;
```

---

### **4. Add New Employee**

**Purpose:**

Inserts a new employee record into the `employee` table. This query handles the addition of employee details such as name, phone number, salary, start date, and department.

**Query Code:**

```sql
INSERT INTO employee (FullName, PhoneNumber, Salary, StartDate, DepartmentID)
VALUES (:full_name, :phone_number, :salary, :start_date, :department_id);
```

---

### **5. Add New User for Employee**

**Purpose:**

Creates a new login record for an employee by inserting the employee ID, email, role, and password into the `user` table.

**Query Code:**

```sql
INSERT INTO user (EmployeeID, Email, Role, Password)
VALUES (:employee_id, :email, :role, :password);
```

---

### **6. Update Employee Information**

**Purpose:**

Updates an employee's information, including name, department, phone number, start date, and salary. This query is crucial for maintaining accurate and up-to-date employee records.

**Query Code:**

```sql
UPDATE employee
SET
    FullName = :full_name,
    DepartmentID = :department_id,
    PhoneNumber = :phone_number,
    StartDate = :start_date,
    Salary = :salary
WHERE
    EmployeeID = :employee_id;
```

---

### **7. Update User Credentials**

**Purpose:**

Updates the email and password of an employee in the `user` table. This is often used when users request to change their credentials.

**Query Code:**

```sql
UPDATE user
SET
    Email = :email,
    Password = :password
WHERE
    EmployeeID = :employee_id;
```

---

### **8. Delete Employee Record**

**Purpose:**

Deletes an employee record based on the provided Employee ID. This action also triggers cascading deletions or constraints in related tables if foreign key dependencies are configured.

**Query Code:**

```sql
DELETE FROM employee
WHERE EmployeeID = :employee_id;
```

---

## **Department Management**

### **9. Get Department IDs and Names**

**Purpose:**

Fetches department IDs and names, primarily for populating dropdowns or performing lookups when assigning employees to departments.

**Query Code:**

```sql
SELECT
    DepartmentID,
    DepartmentName
FROM
    department;
```

---

### 10. Query Title: Retrieve All Departments

**Purpose:**

This query retrieves a list of all departments, their descriptions, and the name of the manager overseeing each department. Additionally, it counts the number of employees within each department.

**Query Code:**

```sql
SELECT
    d.DepartmentName,
    d.Description,
    e.FullName AS Manager,
    COUNT(emp.EmployeeID) AS employee_count
FROM department d
LEFT JOIN employee e ON d.ManagerID = e.EmployeeID
LEFT JOIN employee emp ON d.DepartmentID = emp.DepartmentID
GROUP BY d.DepartmentID;
```

---

### 11. Query Title: Update Department Details

**Purpose:**

Updates the name, manager, and description of a specified department. This query is useful for modifying existing department information.

**Query Code:**

```sql
UPDATE department
SET
    DepartmentName = :department_name,
    ManagerID = :manager_id,
    Description = :description
WHERE DepartmentID = :department_id;
```

---

### 12. Query Title: Delete Department

**Purpose:**

Deletes a department from the database based on its unique ID. This is typically used when a department is no longer needed.

**Query Code:**

```sql
DELETE FROM department
WHERE DepartmentID = :department_id;
```

---

### 13. Query Title: Add New Department

**Purpose:**

Inserts a new department into the database by providing the department name, description, and manager ID.

**Query Code:**

```sql
INSERT INTO department (DepartmentName, Description, ManagerID)
VALUES (:department_name, :description, :manager_id);
```

---

## Attendance Management

### 14. Query Title: Retrieve Attendance with Employee Information

**Purpose:**

Retrieves detailed attendance records by joining employee information. This query provides the full name of employees, attendance dates, and check-in/out times.

**Query Code:**

```sql
SELECT
    a.AttendanceID,
    e.FullName,
    a.AttendanceDate,
    a.CheckInTime,
    a.CheckOutTime
FROM attendance a
INNER JOIN employee e ON e.EmployeeID = a.EmployeeID;
```

---

### 15. Query Title: Update Attendance Record

**Purpose:**

Updates the check-in and check-out times for a specific attendance record. This is useful for correcting or modifying attendance entries.

**Query Code:**

```sql
UPDATE attendance
SET
    CheckInTime = :check_in_time,
    CheckOutTime = :check_out_time
WHERE
    AttendanceID = :attendance_id
    AND AttendanceDate = :attendance_date;
```

---

### 16. Query Title: Employee Check-In

**Purpose:**

Records an employee’s check-in time by inserting a new attendance record with the current date and time. This query is part of the attendance tracking system.

**Query Code:**

```sql
INSERT INTO Attendance (EmployeeID, AttendanceDate, CheckInTime)
VALUES (:employee_id, CURDATE(), CURRENT_TIME());
```

---

### 17. Query Title: Retrieve Check-In and Check-Out Times

**Purpose:**

Fetches the check-in and check-out times for a specific employee on a particular date. This query helps in tracking work hours and attendance.

**Query Code:**

```sql
SELECT
    CheckInTime,
    CheckOutTime
FROM attendance
WHERE
    EmployeeID = :employee_id
    AND AttendanceDate = :attendance_date;
```

---

## Payroll Management

### 18. Query Title: Retrieve Payroll Information

**Purpose:**

Fetches payroll details for all employees, showing their salaries, bonuses, deductions, and net pay. The query joins payroll records with employee and department data.

**Query Code:**

```sql
SELECT
    p.PaymentID,
    e.FullName,
    d.DepartmentName,
    p.Month,
    e.Salary,
    p.Base,
    p.Bonus,
    p.Deduction,
    p.Net
FROM payroll p
LEFT JOIN employee e ON e.EmployeeID = p.EmployeeID
LEFT JOIN department d ON d.DepartmentID = e.DepartmentID;
```

---

### 19. Query Title: Update Payroll Record

**Purpose:**

Modifies the base salary, bonus, and deduction for a specific payroll entry. This query is essential for adjusting payroll records.

**Query Code:**

```sql
UPDATE payroll
SET
    Base = :base_salary,
    Bonus = :bonus,
    Deduction = :deduction
WHERE PaymentID = :payment_id;
```

---

### 20. Query Title: Generate Payslip

**Purpose:**

Inserts a new payroll record to generate a payslip for an employee, calculating net pay based on base salary, bonuses, and deductions.

**Query Code:**

```sql
INSERT INTO payroll (EmployeeID, Month, Base, Bonus, Deduction, Net)
VALUES (:employee_id, :month, :base_salary, :bonus, :deduction, :net_pay);
```

---

### 21. Query Title: Retrieve Payroll Records for Employee

**Purpose:**

Fetches payroll records for a specific employee, showing salary details, bonuses, deductions, and net pay. This query is useful for generating payslips or reviewing payroll history.

**Query Code:**

```sql
SELECT
    p.Month,
    e.Salary,
    p.Bonus,
    p.Deduction,
    p.Net
FROM payroll p
INNER JOIN employee e ON p.EmployeeID = e.EmployeeID
WHERE p.EmployeeID = :employee_id;
```

---

## User Management

### 22. Query Title: Update User Password

**Purpose:**

Updates the password for a specific user identified by their `UserID`. This query is essential for password resets or security updates.

**Query Code:**

```sql
UPDATE user
SET password = :new_password
WHERE UserID = :user_id;
```

---

### 23. Query Title: Update User Email

**Purpose:**

Modifies the email address of a user based on their unique `UserID`. This query is typically used when users request email changes.

**Query Code:**

```sql
UPDATE user
SET Email = :new_email
WHERE UserID = :user_id;
```

---

## Dashboard Statistics

### **24. General Statistics for Dashboard**

**Purpose:**

This query retrieves key statistical data for the dashboard, including total employees, total salaries, total departments, and total net salaries. It provides a quick overview of organizational metrics.

**Query Code:**

```sql
SELECT
    (SELECT COUNT(*) FROM employee) AS TotalEmployees,
    (SELECT SUM(Salary) FROM employee) AS TotalSalaries,
    (SELECT COUNT(*) FROM department) AS TotalDepartments,
    (SELECT SUM(Net) FROM payroll) AS TotalNetSalaries;
```

---

### **25. Attendance Overview**

**Purpose:**

Counts the total number of attendance records for a specific date. Useful for daily monitoring of employee attendance.

**Query Code:**

```sql
SELECT COUNT(*) AS Total_attendance
FROM attendance
WHERE AttendanceDate = :attendance_date;
```

---

### 26. Query Title: Attendance Overview for Employee

**Purpose:**

Counts the total number of attendance records for a specific employee. This query provides a summary of the employee’s attendance.

**Query Code:**

```sql
SELECT COUNT(*) AS Total_attendance
FROM attendance
WHERE EmployeeID = :employee_id;
```

---
