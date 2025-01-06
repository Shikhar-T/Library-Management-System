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


````markdown
```sql
-- Table Creation

-- 1. Branch Table
-- This table stores information about the different branches of the library.
DROP TABLE IF EXISTS branch ;
CREATE TABLE branch 
( 
  branch_id VARCHAR(10) PRIMARY KEY ,
  manager_id VARCHAR(10) ,
  branch_address VARCHAR(55),
  contact_no VARCHAR(10)
);

-- 2. Employee Table
-- This table stores the details of employees who manage the library and its operations.
DROP TABLE IF EXISTS employee ;
CREATE TABLE employee  
(
  emp_id VARCHAR(10) PRIMARY KEY ,
  emp_name VARCHAR(25) ,
  position VARCHAR(25) ,
  salary FLOAT,
  branch_id VARCHAR(25) -- Foreign Key
);

-- 3. Books Table
-- This table stores the details of books available in the library.
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

-- 4. Members Table
-- This table stores information about the members who are registered with the library.
DROP TABLE IF EXISTS members ;
CREATE TABLE members  
(
  member_id VARCHAR(10) PRIMARY KEY ,
  member_name VARCHAR(25) ,
  member_address VARCHAR(75) ,
  reg_date DATE 
);

-- 5. Issued Status Table
-- This table tracks the status of books issued to members, including the employee who issued the book and the issue date.
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

-- 6. Return Status Table
-- This table stores the return status of the books issued to members, including the return date and book quality.
DROP TABLE IF EXISTS return_status ;
CREATE TABLE return_status  
( 
  return_id VARCHAR(10) PRIMARY KEY ,
  issued_id VARCHAR(10) ,
  return_book_name VARCHAR(75),
  return_date DATE,
  return_book_isbn VARCHAR(20)
);

-- Foreign Key Constraints

-- 1. Foreign Keys in Issued Status Table
-- The issued_member_id references the member_id in the members table.
ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);

-- The issued_book_isbn references the isbn in the books table.
ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

-- The issued_emp_id references the emp_id in the employee table.
ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employee(emp_id);

-- 2. Foreign Key in Employee Table
-- The branch_id references the branch_id in the branch table.
ALTER TABLE employee
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);

-- 3. Foreign Key in Return Status Table
-- The issued_id references the issued_id in the issued_status table.
ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);
````


## Objectives of Each Task

### Task 1: Create a New Book Record
- **Objective**: Insert a new book ("To Kill a Mockingbird") into the books table with details like ISBN, title, category, rental price, availability, author, and publisher.

### Task 2: Update an Existing Member's Address
- **Objective**: Update the address of a specific library member (ID 'C101').

### Task 3: Delete a Record from the Issued Status Table
- **Objective**: Remove a record from the issued status table for the book issued to a member (issued_id 'IS121').

### Task 4: Retrieve All Books Issued by a Specific Employee
- **Objective**: Retrieve all books issued by an employee with the employee ID 'E101'.

### Task 5: List Members Who Have Issued More Than One Book
- **Objective**: Identify members who have issued more than one book using the `GROUP BY` clause.

### Task 6: Create Summary Tables - Book Issued Count
- **Objective**: Create a summary table showing the count of books issued for each book using the `CREATE TABLE AS SELECT` (CTAS) technique.

### Task 7: Retrieve All Books in a Specific Category
- **Objective**: Fetch all books belonging to a particular category, e.g., 'Classic'.

### Task 8: Find Total Rental Income by Category
- **Objective**: Calculate the total rental income generated by each category of books.

### Task 9: List Members Who Registered in the Last 180 Days
- **Objective**: Fetch members who have registered in the last 180 days.

### Task 10: List Employees with Their Branch Manager's Name and Branch Details
- **Objective**: Retrieve a list of employees along with their branch manager's name and branch details.

### Task 11: Create a Table of Books with Rental Price Above a Certain Threshold
- **Objective**: Create a table of books where rental price is above $7.

### Task 12: Retrieve the List of Books Not Yet Returned
- **Objective**: Fetch all books that are issued but have not been returned yet.

### Task 13: Identify Members with Overdue Books
- **Objective**: Identify members who have overdue books (i.e., not returned within 30 days).

### Task 14: Branch Performance Report
- **Objective**: Generate a performance report for each branch, showing the number of books issued, number of books returned, and total revenue generated.

### Task 15: Create a Table of Active Members
- **Objective**: Use CTAS to create a table containing active members who have issued at least one book in the last 6 months.

### Task 16: Find Employees with the Most Book Issues Processed
- **Objective**: Retrieve the top 3 employees who have processed the most book issues, showing the employee's name, the number of books processed, and their branch.

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
