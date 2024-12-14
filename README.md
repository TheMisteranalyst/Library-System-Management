# Library-System-Management using SQL
### Library Management System 📚   
**Database:** `library_db`  

This project demonstrates the design and implementation of a **Library Management System** using SQL, focusing on real-world database management skills.  

#### **Project Features**
- **Database Setup**:  
  Created and populated tables for essential entities such as branches, employees, members, books, issued status, and return status.  

- **CRUD Operations**:  
  Implemented **Create**, **Read**, **Update**, and **Delete** operations to manipulate data efficiently.  

- **CTAS (Create Table As Select)**:  
  Used CTAS to generate new tables dynamically based on query results for advanced analysis.  

- **Advanced SQL Queries**:  
  Developed and executed complex queries to analyze data, such as tracking overdue books, calculating fines, and identifying high-risk issued books.

#### **Skills Demonstrated**
- Database design and normalization.
- Proficiency in SQL, including joins, subqueries, and aggregate functions.
- Analytical capabilities using advanced SQL techniques.
- Effective data management and problem-solving in library operations.

# Project structure
#### 1. Database Schema

Below is the database schema for the Library Management System:


![Library Management System Schema](Library%20system%20management%20project.pgerd.png)


- **Database Creation:** Created a database named Library_Management_Project.
- **Table Creation:** Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

## Library Management System - SQL Schema

Below is the SQL code for setting up the database schema of the Library Management System:

```sql
-- Library Management System Project
DROP TABLE IF EXISTS branch;
CREATE TABLE branch
         (
		   branch_id VARCHAR(10) PRIMARY KEY,
		   manager_id VARCHAR(10),
		   branch_address VARCHAR(50),
		   contact_no VARCHAR(20)
		   );

DROP TABLE IF EXISTS employees;
CREATE TABLE employees
         (emp_id VARCHAR(10)  PRIMARY KEY,
		  emp_name VARCHAR(20), 
		  position VARCHAR(10),
		  salary INT,
		  branch_id VARCHAR(10) --fk
         );

DROP TABLE IF EXISTS books;
CREATE TABLE books
         (isbn VARCHAR(30) PRIMARY KEY,
		  book_title VARCHAR(80),
		  category VARCHAR(20),
		  rental_price FLOAT,
		  status VARCHAR(10),
		  author VARCHAR(25),
		  publisher VARCHAR(35)
		  );

DROP TABLE IF EXISTS members;
CREATE TABLE members
         (member_id VARCHAR(20) PRIMARY KEY,
		  member_name VARCHAR(25),
		  member_address VARCHAR(60),
		  reg_date DATE
          );

DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status
         (issued_id	VARCHAR(10) PRIMARY KEY,
		  issued_member_id VARCHAR(10), --fk
		  issued_book_name	VARCHAR(85),
		  issued_date DATE,
		  issued_book_isbn VARCHAR(30), --fk
		  issued_emp_id VARCHAR(10) --fk
		 );

DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status
         (return_id	VARCHAR(10) PRIMARY KEY,
		  issued_id VARCHAR(10),
		  return_book_name VARCHAR(80),
		  return_date DATE,
		  return_book_isbn VARCHAR(10)
		  );

--FOREIGN KEY
ALTER TABLE issued_status 
ADD CONSTRAINT fk_members 
FOREIGN KEY (issued_member_id) 
REFERENCES members(member_id);

ALTER TABLE issued_status 
ADD CONSTRAINT fk_books 
FOREIGN KEY (issued_book_isbn) 
REFERENCES books(isbn);		

ALTER TABLE issued_status 
ADD CONSTRAINT fk_employees 
FOREIGN KEY (issued_emp_id) 
REFERENCES employees(emp_id);	

ALTER TABLE employees 
ADD CONSTRAINT fk_branch 
FOREIGN KEY (branch_id) 
REFERENCES branch(branch_id);

ALTER TABLE return_status 
ADD CONSTRAINT fk_issue_status 
FOREIGN KEY (issued_id) 
REFERENCES issued_status(issued_id);
```
### 2. CRUD Operations
- **Create:** Inserted sample records into the `books` table.
- **Read:** Retrieved and displayed data from various tables.
- **Update:** Updated records in the `employees` table.
- **Delete:** Removed records from the `members` table as needed.

### Task 1. Create a New Book Record :- 
**('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')**

```sql
INSERT INTO books(isbn,book_title,category,rental_price,status,author,publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird',
'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

### Task 2: Update an Existing Member's Address
```sql
UPDATE members
SET member_address='459 Elm st'
WHERE member_id='C102';
```
### Task 3: Delete a Record from the Issued Status Table Objective: Delete the record with issued_id = 'IS121' from the issued_status table.
```sql
DELETE FROM issued_status
WHERE issued_id = 'IS121';
```

This project highlights my ability to work with databases and solve practical challenges through robust and efficient SQL implementations.


**Check out the project files and SQL scripts in the repository!**
