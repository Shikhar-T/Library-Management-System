# Library Management System

## Overview
This is a comprehensive Library Management System built using SQL, which includes multiple tables and queries to manage books, members, employees, branches, issued and returned books, and their corresponding status. The system allows for operations such as creating, updating, and deleting records, as well as generating reports and summaries based on specific requirements.

## Database Schema
The following tables have been created for managing the library data:

1. **Branch** - Stores information about the library branches.
2. **Employee** - Stores employee details.
3. **Books** - Stores book details in the library.
4. **Members** - Stores information about library members.
5. **Issued Status** - Tracks the issued books to members.
6. **Return Status** - Tracks the returned books.

# **Table Creation**


1. Branch Table
-- This table stores information about the different branches of the library.
```sql
DROP TABLE IF EXISTS branch ;
CREATE TABLE branch 
( 
  branch_id VARCHAR(10) PRIMARY KEY ,
  manager_id VARCHAR(10) ,
  branch_address VARCHAR(55),
  contact_no VARCHAR(10)
);
```

2. Employee Table
-- This table stores the details of employees who manage the library and its operations.
```sql
DROP TABLE IF EXISTS employee ;
CREATE TABLE employee  
(
  emp_id VARCHAR(10) PRIMARY KEY ,
  emp_name VARCHAR(25) ,
  position VARCHAR(25) ,
  salary FLOAT,
  branch_id VARCHAR(25) -- Foreign Key
);
```

3. Books Table
-- This table stores the details of books available in the library.
```sql
DROP TABLE IF EXISTS books ;
CREATE TABLE books  
(
  isbn VARCHAR(20) PRIMARY KEY ,
  book_title VARCHAR(60) ,
  category VARCHAR(20) ,
  rental_price FLOAT,
  status VARCHAR(15) ,
  author VARCHAR(30) ,	
  publisher VARCHAR(30)
);
```

4. Members Table
-- This table stores information about the members who are registered with the library.
```sql
DROP TABLE IF EXISTS members ;
CREATE TABLE members  
(
  member_id VARCHAR(10) PRIMARY KEY ,
  member_name VARCHAR(25) ,
  member_address VARCHAR(75) ,
  reg_date DATE 
);
```

5. Issued Status Table
-- This table tracks the status of books issued to members, including the employee who issued the book and the issue date.
```sql
DROP TABLE IF EXISTS issued_status ;
CREATE TABLE issued_status  
( 
  issued_id VARCHAR(10) PRIMARY KEY,	
  issued_member_id VARCHAR(10),   -- Foreign Key
  issued_book_name VARCHAR(75),
  issued_date DATE ,	
  issued_book_isbn VARCHAR(25),	-- Foreign Key
  issued_emp_id VARCHAR(10)       -- Foreign Key
);
```

6. Return Status Table
-- This table stores the return status of the books issued to members, including the return date and book quality.
```sql
DROP TABLE IF EXISTS return_status ;
CREATE TABLE return_status  
( 
  return_id VARCHAR(10) PRIMARY KEY ,
  issued_id VARCHAR(10) ,
  return_book_name VARCHAR(75),
  return_date DATE,
  return_book_isbn VARCHAR(20)
);
```

# **Foreign Key Constraints**


1. Foreign Keys in Issued Status Table

1.1) The issued_member_id references the member_id in the members table.
```sql
ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);
```
1.2) The issued_book_isbn references the isbn in the books table.
```sql
ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);
```
1.3) The issued_emp_id references the emp_id in the employee table.
```sql
ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employee(emp_id);
```

2. Foreign Key in Employee Table
-- The branch_id references the branch_id in the branch table.
```sql
ALTER TABLE employee
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);
```

3. Foreign Key in Return Status Table
-- The issued_id references the issued_id in the issued_status table.
```sql
ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);
```

## Objectives of Each Task

### Task 1: Create a New Book Record
- **Objective**: Insert a new book ("To Kill a Mockingbird") into the books table with details like ISBN, title, category, rental price, availability, author, and publisher.
```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```

### Task 2: Update an Existing Member's Address
- **Objective**: Update the address of a specific library member (ID 'C101').
```sql
UPDATE members
SET member_address = '125 Main St'
WHERE member_id = 'C101';
SELECT * FROM members ;
```

### Task 3: Delete a Record from the Issued Status Table
- **Objective**: Remove a record from the issued status table for the book issued to a member (issued_id 'IS121').
```sql
DELETE FROM issued_status 
WHERE issued_id = 'IS121';
SELECT * FROM issued_status;
```

### Task 4: Retrieve All Books Issued by a Specific Employee
- **Objective**: Retrieve all books issued by an employee with the employee ID 'E101'.
```sql
SELECT * 
FROM issued_status
WHERE issued_emp_id = 'E101';
```

### Task 5: List Members Who Have Issued More Than One Book
- **Objective**: Identify members who have issued more than one book using the `GROUP BY` clause.

```sql
SELECT issued_emp_id , COUNT(issued_emp_id) AS no_of_books
FROM issued_status 
GROUP BY issued_emp_id
HAVING COUNT(issued_emp_id) > 1 ;
```

### Task 6: Create Summary Tables - Book Issued Count
- **Objective**: Create a summary table showing the count of books issued for each book using the `CREATE TABLE AS SELECT` (CTAS) technique.

```sql
CREATE TABLE book_issued_cnt 
SELECT isbn , book_title , COUNT(issued_id)
FROM books AS b
JOIN issued_status AS ist
ON ist.issued_book_isbn = b.isbn 
GROUP BY isbn , book_title ;

SELECT * FROM book_issued_cnt ;

```

### Task 7: Retrieve All Books in a Specific Category
- **Objective**: Fetch all books belonging to a particular category, e.g., 'Classic'.
```sql
SELECT book_title , category 
FROM books 
WHERE category = 'Classic' ;
```

### Task 8: Find Total Rental Income by Category
- **Objective**: Calculate the total rental income generated by each category of books.
```sql
SELECT category , SUM(rental_price) , COUNT(issued_book_isbn)
FROM books AS b
JOIN issued_status AS a
ON b.isbn = a.issued_book_isbn
GROUP BY category 
ORDER BY SUM(rental_price) DESC;

```
### Task 9: List Members Who Registered in the Last 180 Days
- **Objective**: Fetch members who have registered in the last 180 days.
```sql
SELECT *
FROM members
WHERE reg_date >= CURDATE() - INTERVAL 180 DAY ;
```

### Task 10: List Employees with Their Branch Manager's Name and Branch Details
- **Objective**: Retrieve a list of employees along with their branch manager's name and branch details.
```sql
SELECT e1.* , e2.emp_name AS manager , b.manager_id 
FROM employee AS e1
JOIN branch AS b
ON b.branch_id = e1.branch_id
JOIN employee AS e2
ON b.manager_id = e2.emp_id ;
```

### Task 11: Create a Table of Books with Rental Price Above a Certain Threshold
- **Objective**: Create a table of books where rental price is above $7.
```sql
CREATE TABLE books_rentalprice
AS 
   SELECT book_title , rental_price
	FROM books
	WHERE rental_price > 7;
SELECT * FROM books_rentalprice ;
```

### Task 12: Retrieve the List of Books Not Yet Returned
- **Objective**: Fetch all books that are issued but have not been returned yet.
```sql
SELECT *
 FROM issued_status AS b
 LEFT JOIN return_status AS a
 ON b.issued_id = a.issued_id 
 WHERE a.return_id IS NULL ;
 
-- Adding new values in issued_status
INSERT INTO issued_status 
(issued_id, issued_member_id, issued_book_name, issued_date, issued_book_isbn, issued_emp_id)
VALUES 
('IS151', 'C118', 'The Catcher in the Rye', DATE_SUB(CURRENT_DATE, INTERVAL 24 DAY), '978-0-553-29698-2', 'E108'),
('IS152', 'C119', 'The Catcher in the Rye', DATE_SUB(CURRENT_DATE, INTERVAL 13 DAY), '978-0-553-29698-2', 'E109'),
('IS153', 'C106', 'Pride and Prejudice', DATE_SUB(CURRENT_DATE, INTERVAL 7 DAY), '978-0-14-143951-8', 'E107'),
('IS154', 'C105', 'The Road', DATE_SUB(CURRENT_DATE, INTERVAL 32 DAY), '978-0-375-50167-0', 'E101');


-- Adding new column in return_status

ALTER TABLE return_status
ADD Column book_quality VARCHAR(15) DEFAULT('Good');

UPDATE return_status
SET book_quality = 'Damaged'
WHERE issued_id 
    IN ('IS112', 'IS117', 'IS118');
SELECT * FROM return_status;

```
### Task 13: Identify Members with Overdue Books
- **Objective**: Identify members who have overdue books (i.e., not returned within 30 days).
```sql
SELECT ist.issued_member_id,  m.member_name, bk.book_title , ist.issued_date,
current_date() - ist.issued_date as book_over_due
FROM issued_status AS ist
JOIN members AS m
ON m.member_id = ist.issued_member_id
JOIN books AS bk
ON bk.isbn = ist.issued_book_isbn
JOIN return_status AS rs
ON rs.issued_id = ist.issued_id
WHERE
(CURRENT_DATE - ist.issued_date) > 30
ORDER BY ist.issued_member_id ;

```
### Task 14: Branch Performance Report
- **Objective**: Generate a performance report for each branch, showing the number of books issued, number of books returned, and total revenue generated.
```sql
CREATE TABLE branch_reports
AS

SELECT b.branch_id, b.manager_id, 
COUNT(ist.issued_id) AS no_of_book_issued ,COUNT(rs.return_id) AS no_of_book_returned
,SUM(bk.rental_price) AS total_revenue 
FROM issued_status AS ist 
JOIN employee AS e
ON e.emp_id= ist.issued_emp_id
JOIN 
branch AS b
ON e.branch_id = b.branch_id
LEFT JOIN return_status AS rs
ON rs.issued_id = ist.issued_id
JOIN books AS bk 
ON ist.issued_book_isbn = bk.isbn
GROUP BY b.branch_id,b.manager_id;

SELECT * FROM branch_reports ;

```
### Task 15: Create a Table of Active Members
- **Objective**: Use CTAS to create a table containing active members who have issued at least one book in the last 6 months.
```sql
CREATE TABLE active_members AS
SELECT * 
FROM members
WHERE member_id IN 
(
    SELECT issued_member_id
    FROM issued_status
    WHERE issued_date > CURRENT_DATE - INTERVAL 6 MONTH
);

SELECT * FROM active_members ;

```

### Task 16: Find Employees with the Most Book Issues Processed
- **Objective**: Retrieve the top 3 employees who have processed the most book issues, showing the employee's name, the number of books processed, and their branch.
```sql
SELECT e.emp_name , b.* , COUNT(ist.issued_id)
FROM issued_status AS ist
JOIN employee as e
ON e.emp_id = ist.issued_emp_id
JOIN branch AS b
ON e.branch_id = b.branch_id
GROUP BY 1,2 ;

```
---

## SQL Code

The full set of SQL commands for the Library Management System is included in the project. Each SQL statement is designed to perform specific operations like inserting data, querying for information, or generating reports.

---

## Getting Started

To run this project:
1. Ensure you have a MySQL or similar SQL database management system installed.
2. Execute the provided SQL scripts in your SQL environment to set up the tables.
3. Run the queries to perform the tasks as described above.

---

Feel free to contribute to the project or modify it according to your requirements!
