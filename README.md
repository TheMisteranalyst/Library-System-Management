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
### 1. Database Schema

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
### Task 3: Delete a Record from the Issued Status Table
**Objective: Delete the record with issued_id = 'IS121' from the issued_status table.**
```sql
DELETE FROM issued_status
WHERE issued_id = 'IS121';
```
### Task 4: Retrieve All Books Issued by a Specific Employee
**Objective: Select all books issued by the employee with emp_id = 'E101'.**
```sql
SELECT * FROM issued_status where issued_emp_id='E101';
```

### Task 5: List Members Who Have Issued More Than One Book
**Objective: Use GROUP BY to find members who have issued more than one book.**
```sql

SELECT members.member_id, members.member_name,COUNT(issued_status.issued_member_id)
FROM members
JOIN issued_status ON members.member_id = issued_status.issued_member_id
GROUP BY members.member_id, members.member_name
HAVING COUNT(issued_status.issued_member_id) > 1;
```
### 3. CTAS (Create Table As Select)
### Task 6: Create Summary Tables:
**Used CTAS to generate new tables based on query results each book and total book_issued_cnt**
```sql

CREATE TABLE book_cnt
AS 
SELECT b.isbn,b.book_title,COUNT(ist.issued_book_name)
FROM books as b
JOIN issued_status  as ist
ON ist.issued_book_isbn = b.isbn
GROUP BY 1,2;

SELECT * FROM book_cnt;
```
## 4. Data Analysis & Findings
### Task 7. Retrieve All Books in a Specific Category:

```sql
SELECT * FROM books WHERE category='Classic';
```

### Task 8: Find Total Rental Income by Category:
```sql
SELECT b.category,sum(rental_price),count(*)
FROM books as b
JOIN issued_status  as ist
ON ist.issued_book_isbn = b.isbn
GROUP BY 1;
```
### Task 9: List Members Who Registered in the Last 180 Days:
```sql
INSERT INTO members(member_id,member_name,member_address,reg_date)
VALUES('C120','Ron Weasley','655 Pine St','2024-09-24'),
('C121','Harry Potter','695 Oak St','2024-10-20');

SELECT * FROM members WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```
### Task 10: List Employees with Their Branch Manager's Name and their branch details:
```sql
SELECT * FROM employees;
SELECT * FROM branch;

SELECT e1.*,
b.manager_id,
e2.emp_name as Manager_Name,
b.branch_address
FROM employees as e1
JOIN branch as b
ON b.branch_id = e1.branch_id
JOIN employees as e2
ON e2.emp_id=b.manager_id;
```
### Task 11. Create a Table of Books with Rental Price Above a Certain Threshold:
```sql
CREATE TABLE books_rental_more_than_six
AS
SELECT * FROM books
WHERE rental_price>=6;
```

### Task 12: Retrieve the List of Books Not Yet Returned
```sql
SELECT * FROM issued_status
SELECT * FROM return_status

SELECT 
DISTINCT ist.issued_book_name
FROM issued_status as ist
LEFT JOIN return_status as rs
ON ist.issued_id=rs.issued_id
WHERE rs.return_id IS NULL
```
# Advanced Sql Operations
### Task 13: Identify Members with Overdue Books
Write a query to identify members who have overdue books (assume a 60-day return period). 
Display the member's_id, member's name, book title, issue date, and days overdue.
```sql
SELECT
m.member_id,
m.member_name,
bk.book_title,
ist.issued_date,
rs.return_date,
CURRENT_DATE - ist.issued_date as overdue_days
FROM issued_status as ist
JOIN members as m
ON ist.issued_member_id=m.member_id
JOIN books as bk
ON ist.issued_book_isbn=bk.isbn
LEFT JOIN return_status as rs
ON ist.issued_id=rs.issued_id
WHERE 
rs.return_date IS NULL
AND
(CURRENT_DATE - ist.issued_date)>=60;
```
### Task 14: Update Book Status on Return
Write a query to update the status of books in the books table to "Yes" 
when they are returned (based on entries in the return_status table).
```sql
--manual way of updating status
SELECT * FROM return_status
where issued_id='IS154';

SELECT * FROM books
where isbn='978-0-375-50167-0';

SELECT * FROM issued_status as ist
where ist.issued_book_isbn='978-0-375-50167-0';

UPDATE books
SET status='No'
WHERE isbn='978-0-375-50167-0'

INSERT INTO return_status(return_id,issued_id,return_date,book_quality)
VALUES('RS152','IS154',CURRENT_DATE,'Good')


UPDATE books
SET status='yes'
WHERE isbn='978-0-375-50167-0';
SELECT * FROM books
where isbn='978-0-375-50167-0'

-- Store procedures
CREATE OR REPLACE PROCEDURE add_return_records(p_return_id VARCHAR(10),p_issued_id VARCHAR(10),p_book_quality VARCHAR(15))
LANGUAGE plpgsql
AS $$
DECLARE
        v_isbn VARCHAR(30);
		v_book_name VARCHAR(85);

BEGIN
     --Inserting into return based on user input 
     INSERT INTO return_status(return_id,issued_id,return_date,book_quality)
     VALUES(p_return_id,p_issued_id,CURRENT_DATE,p_book_quality);


     SELECT
	 ist.issued_book_isbn,
	 issued_book_name
	 INTO
	 v_isbn,v_book_name
	 FROM issued_status as ist 
	 WHERE issued_id=p_issued_id;

	 
     UPDATE books
     SET status='yes'
     WHERE isbn=v_isbn;

	 RAISE NOTICE 'Thank You For Returning the book: %',v_book_name;   --RAISE NOTICE WORK LIKE PRINT AND IT WILL PRINT WHAT YOU WANT
END;
$$ 

-- Testing function add_return_records
SELECT * FROM books
WHERE isbn='978-0-7432-7357-1'

SELECT * FROM issued_status
where issued_book_isbn='978-0-7432-7357-1'

SELECT * FROM return_status
WHERE issued_id='IS136'


CALL add_return_records('RS139','IS136','Good');
```
### Task 15: Branch Performance Report
Create a query that generates a performance report for each branch, showing the number of books issued, 
the number of books returned, and the total revenue generated from book rentals.
```sql
CREATE TABLE branch_revenue_report
AS 
SELECT 
      b.branch_id,
      b.manager_id,
	  COUNT(ist.issued_id) as number_of_book_issued,
	  COUNT(rs.return_id) as number_of_book_return,
	  SUM(bk.rental_price) as Total_branch_revenue
FROM issued_status as ist
JOIN employees as e
ON e.emp_id=ist.issued_emp_id
JOIN branch as b
ON e.branch_id=b.branch_id
LEFT JOIN 
return_status as rs
ON rs.issued_id=ist.issued_id
JOIN books as bk 
ON ist.issued_book_isbn=bk.isbn
GROUP BY 1,2;

SELECT * FROM branch_revenue_report
```
### Task 16: CTAS: Create a Table of Active Members
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members 
who have issued at least one book in the last 1 YEAR.
```sql
CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN (SELECT
                    DISTINCT issued_member_id
					FROM issued_status
					WHERE 
					issued_date>= CURRENT_DATE - INTERVAL'1YEAR'
                    );
```

### Task 17: Find Employees with the Most Book Issues Processed
Write a query to find the top 3 employees who have processed the most book issues.
Display the employee name, number of books processed, and their branch.

```sql
SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) as no_book_issued
FROM issued_status as ist
JOIN
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2;
```
### Task 18: Identify Members Issuing High-Risk Books
Write a query to identify members who have issued books more than twice with the status "damaged" in the books table. 
Display the member name, book title, and the number of times they've issued damaged books.

```sql
SELECT * FROM books

SELECT * FROM return_status
SELECT * FROM members
SELECT * FROM issued_status


SELECT 
    DISTINCT
	m.member_name,
    b.book_title,
    COUNT(ist.issued_id) AS no_books_issued
FROM 
    issued_status AS ist
JOIN
    members AS m
ON 
    m.member_id = ist.issued_member_id
JOIN
    books AS b
ON 
    ist.issued_book_isbn = b.isbn
LEFT JOIN 
    return_status AS rs
ON 
    rs.issued_id = ist.issued_id
WHERE 
    rs.book_quality ='Damaged'  
GROUP BY 
    m.member_name, b.book_title
HAVING 
    COUNT(ist.issued_id) >2;
```
### Task 19: Stored Procedure Objective: 
Create a stored procedure to manage the status of books in a library system.Description: Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows: The stored procedure should take the book_id as an input parameter. The procedure should first check if the book is available (status = 'yes').If the book is available, it should be issued, and the status in the books table should be updated to 'no'. If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.	
This project highlights my ability to work with databases and solve practical challenges through robust and efficient SQL implementations.
```sql
SELECT * FROM issued_status;
SELECT * FROM books;

CREATE OR REPLACE PROCEDURE issue_book_status(p_issued_id VARCHAR(10),p_issued_member_id VARCHAR(10),
p_issued_book_isbn VARCHAR(30),p_issued_emp_id VARCHAR(10))
LANGUAGE plpgsql
AS $$
DECLARE 
       v_status VARCHAR(10);
	   v_title VARCHAR(80);

BEGIN
      SELECT status,book_title 
	  INTO v_status,v_title 
	  FROM books
	  WHERE isbn=p_issued_book_isbn;

	  IF v_status ='yes' THEN
	               INSERT INTO issued_status(issued_id,issued_member_id,issued_book_name,issued_date,issued_book_isbn,issued_emp_id)
	               VALUES
	                 (p_issued_id,p_issued_member_id,v_title,CURRENT_DATE,p_issued_book_isbn,p_issued_emp_id);
                   UPDATE books
                   SET status='no'
                   WHERE isbn=p_issued_book_isbn;
      
      RAISE NOTICE 'Book records added successfully for book ISBN: %, Title: %', p_issued_book_isbn, v_title;

	  ELSE
	       RAISE NOTICE 'Sorry! The book you have requested is unavailable. ISBN: %, Title: %', p_issued_book_isbn, v_title;


	  END IF;
END;
$$


-- Testing The function
SELECT * FROM books;
-- ""978-0-14-044930-3"" -- no
-- ""978-0-141-44171-6"" -- yes
SELECT * FROM issued_status;

CALL issue_book_status('IS156', 'C109', '978-0-14-044930-3', 'E108');
CALL issue_book_status('IS156', 'C108', '"978-0-141-44171-6"', 'E104');

SELECT * FROM books
WHERE isbn = '978-0-14-044930-3'

```
### Task 20: Create Table As Select (CTAS) Objective: 
Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines.
Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 60 days. The table should include: The number of overdue books. The total fines, with each day's fine calculated at $0.50. The number of books issued by each member. The resulting table should show: Member ID Number of overdue books Total fines
```sql
CREATE TABLE overdue_books_summary AS
SELECT
    m.member_id,
    m.member_name,
    COUNT(*) AS overdue_books,
    SUM(CASE WHEN CURRENT_DATE - ist.issued_date > 60 AND rs.return_date IS NULL THEN 
        (CURRENT_DATE - ist.issued_date - 60) * 0.50 ELSE 0 END) AS total_fines
FROM issued_status AS ist
JOIN members AS m
ON ist.issued_member_id = m.member_id
LEFT JOIN return_status AS rs
ON ist.issued_id = rs.issued_id
WHERE rs.return_date IS NULL
AND (CURRENT_DATE - ist.issued_date) >= 60
GROUP BY m.member_id;

select * from overdue_books_summary
```
# Reports
- **Database Schema: Detailed table structures and relationships.**
- **Data Analysis: Insights into book categories, employee salaries, member registration trends, and issued books.**
- **Summary Reports: Aggregated data on high-demand books and employee performance.**
# Conclusion
This project demonstrates the practical application of SQL in creating and managing a library management system. It includes tasks such as data insertion, updates, advanced querying, and reporting, offering a comprehensive understanding of database operations.


