# 15-Minute SQL Challenge: The University Database

## Installing MySQL on Linux

Before diving into the challenge, you'll need to set up MySQL on your Linux virtual machine. The installation process varies slightly depending on which Linux distribution you're using. Here are instructions for the most common distributions:

### For Ubuntu/Debian-based distributions:

1. **Update your package index**:
   ```bash
   sudo apt update
   ```

2. **Install MySQL Server**:
   ```bash
   sudo apt install mysql-server
   ```

3. **Start the MySQL service**:
   ```bash
   sudo systemctl start mysql
   ```

4. **Enable MySQL to start on boot** (optional but recommended):
   ```bash
   sudo systemctl enable mysql
   ```

5. **Secure your MySQL installation**:
   ```bash
   sudo mysql_secure_installation
   ```
   This utility will walk you through setting a root password and removing some dangerous defaults.

6. **Verify the installation**:
   ```bash
   sudo systemctl status mysql
   ```
   You should see output indicating that the service is active (running).

### For Red Hat/Fedora/CentOS:

1. **Install MySQL Server** (on newer versions you might need to use MariaDB instead):
   ```bash
   sudo dnf install mysql-server   # For Fedora/newer RHEL
   # OR
   sudo yum install mysql-server   # For CentOS/older RHEL
   ```

2. **Start the MySQL service**:
   ```bash
   sudo systemctl start mysqld
   ```

3. **Enable MySQL to start on boot**:
   ```bash
   sudo systemctl enable mysqld
   ```

4. **Secure your MySQL installation**:
   ```bash
   sudo mysql_secure_installation
   ```

5. **Verify the installation**:
   ```bash
   sudo systemctl status mysqld
   ```

### Logging in to MySQL for the first time:

After installation, you can log in to MySQL as the root user:

```bash
sudo mysql
```

Or if you set a password during the secure installation:

```bash
mysql -u root -p
```

Then enter your password when prompted.

### Troubleshooting Common Installation Issues:

1. **Can't connect to MySQL server**:
   - Check if the service is running: `sudo systemctl status mysql` or `sudo systemctl status mysqld`
   - If it's not running, start it: `sudo systemctl start mysql` or `sudo systemctl start mysqld`

2. **Permission denied**:
   - If you get "Access denied for user 'root'@'localhost'", try:
     ```bash
     sudo mysql -u root
     ```
   - Or reset the root password:
     ```bash
     sudo mysqld_safe --skip-grant-tables &
     sudo mysql -u root
     ```
     Then within MySQL:
     ```sql
     USE mysql;
     ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
     FLUSH PRIVILEGES;
     EXIT;
     ```
     
3. **MySQL daemon failed to start**:
   - Check the error logs: `sudo tail -f /var/log/mysql/error.log` or `sudo journalctl -u mysql`
   - Common issues include insufficient disk space or conflicting port usage

Now that you have MySQL installed and running, you're ready to tackle the challenge!

## Challenge Overview

You are a database administrator for a small university. The registrar has asked you to help set up a simple database system to track students, courses, and enrollments. You'll need to create the database, define the tables, populate them with sample data, and then write queries to answer specific questions about the data.

This challenge is designed to be completed in 15 minutes on a Linux virtual machine with MySQL installed.

## Prerequisites

- A Linux virtual machine with MySQL installed
- Basic knowledge of SQL commands
- Access to the terminal

## Getting Started

1. Open a terminal window in your Linux virtual machine
2. Connect to MySQL using the command:

```bash
mysql -u root -p
```

Enter your password when prompted. If you haven't set a password yet, you may be able to connect without a password using:

```bash
mysql -u root
```

## Step 1: Create the Database

Let's start by creating a new database for our university:

```sql
CREATE DATABASE university;
USE university;
```

## Step 2: Create the Tables

Now, let's create three tables for our database:

```sql
-- Create the students table
CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    major VARCHAR(50),
    credits_earned INT DEFAULT 0
);

-- Create the courses table
CREATE TABLE courses (
    course_id VARCHAR(10) PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    credits INT,
    instructor VARCHAR(100)
);

-- Create the enrollments table
CREATE TABLE enrollments (
    enrollment_id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    course_id VARCHAR(10),
    semester VARCHAR(20),
    grade CHAR(1),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

## Step 3: Insert Sample Data

Let's populate our tables with some sample data:

```sql
-- Insert students
INSERT INTO students (first_name, last_name, email, major, credits_earned) VALUES
('John', 'Smith', 'john.smith@university.edu', 'Computer Science', 60),
('Emma', 'Johnson', 'emma.j@university.edu', 'Biology', 45),
('Michael', 'Williams', 'michael.w@university.edu', 'Mathematics', 90),
('Sophia', 'Brown', 'sophia.b@university.edu', 'English', 30),
('William', 'Jones', 'william.j@university.edu', 'Computer Science', 75),
('Olivia', 'Garcia', 'olivia.g@university.edu', 'Physics', 60),
('James', 'Miller', 'james.m@university.edu', 'Chemistry', 45),
('Ava', 'Davis', 'ava.d@university.edu', 'Mathematics', 90),
('Alexander', 'Rodriguez', 'alex.r@university.edu', 'History', 30),
('Mia', 'Martinez', 'mia.m@university.edu', 'Computer Science', 60);

-- Insert courses
INSERT INTO courses (course_id, title, department, credits, instructor) VALUES
('CS101', 'Introduction to Programming', 'Computer Science', 3, 'Dr. Johnson'),
('BIO201', 'Cell Biology', 'Biology', 4, 'Dr. Smith'),
('MATH301', 'Advanced Calculus', 'Mathematics', 4, 'Dr. Williams'),
('ENG101', 'Composition', 'English', 3, 'Dr. Brown'),
('CS201', 'Data Structures', 'Computer Science', 3, 'Dr. Garcia'),
('PHYS101', 'Physics I', 'Physics', 4, 'Dr. Miller'),
('CHEM101', 'General Chemistry', 'Chemistry', 4, 'Dr. Davis'),
('MATH201', 'Linear Algebra', 'Mathematics', 3, 'Dr. Rodriguez'),
('HIST101', 'World History', 'History', 3, 'Dr. Martinez'),
('CS301', 'Database Systems', 'Computer Science', 3, 'Dr. Adams');

-- Insert enrollments
INSERT INTO enrollments (student_id, course_id, semester, grade) VALUES
(1, 'CS101', 'Fall 2023', 'A'),
(1, 'CS201', 'Fall 2023', 'B'),
(2, 'BIO201', 'Fall 2023', 'A'),
(3, 'MATH301', 'Fall 2023', 'A'),
(4, 'ENG101', 'Fall 2023', 'B'),
(5, 'CS101', 'Fall 2023', 'A'),
(5, 'CS201', 'Fall 2023', 'A'),
(6, 'PHYS101', 'Fall 2023', 'C'),
(7, 'CHEM101', 'Fall 2023', 'B'),
(8, 'MATH201', 'Fall 2023', 'A'),
(9, 'HIST101', 'Fall 2023', 'B'),
(10, 'CS301', 'Fall 2023', 'A'),
(2, 'MATH201', 'Spring 2024', 'B'),
(3, 'CS101', 'Spring 2024', 'A'),
(4, 'HIST101', 'Spring 2024', 'C'),
(6, 'MATH301', 'Spring 2024', 'B'),
(7, 'BIO201', 'Spring 2024', 'A'),
(8, 'CS301', 'Spring 2024', 'A'),
(9, 'ENG101', 'Spring 2024', 'B'),
(10, 'CS201', 'Spring 2024', 'A');
```

## Step 4: Challenge Questions

Now that our database is set up, it's time for the challenge! Write SQL queries to answer the following questions:

1. List all Computer Science majors and the number of credits they've earned, ordered by credits in descending order.

2. Find the average grade point for each course (assume A=4, B=3, C=2, D=1, F=0). Display the course title and the average GPA.

3. Which department offers the most courses?

4. Find all students who are taking courses in the same department as their major.

5. Identify the student(s) with the highest overall GPA across all their courses.

## Solutions

Try to solve the challenges on your own first! Here are the solutions if you need them:

### Question 1: Computer Science Majors by Credits

```sql
SELECT first_name, last_name, credits_earned 
FROM students 
WHERE major = 'Computer Science' 
ORDER BY credits_earned DESC;
```

### Question 2: Average Grade Point by Course

```sql
SELECT c.course_id, c.title, 
  AVG(CASE 
        WHEN e.grade = 'A' THEN 4 
        WHEN e.grade = 'B' THEN 3 
        WHEN e.grade = 'C' THEN 2 
        WHEN e.grade = 'D' THEN 1 
        WHEN e.grade = 'F' THEN 0 
      END) AS average_gpa
FROM courses c
JOIN enrollments e ON c.course_id = e.course_id
GROUP BY c.course_id, c.title
ORDER BY average_gpa DESC;
```

### Question 3: Department with Most Courses

```sql
SELECT department, COUNT(*) as course_count
FROM courses
GROUP BY department
ORDER BY course_count DESC
LIMIT 1;
```

### Question 4: Students Taking Courses in Their Major Department

```sql
SELECT DISTINCT s.first_name, s.last_name, s.major, c.title
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN courses c ON e.course_id = c.course_id
WHERE s.major = c.department;
```

### Question 5: Student(s) with Highest Overall GPA

```sql
SELECT s.student_id, s.first_name, s.last_name,
  AVG(CASE 
        WHEN e.grade = 'A' THEN 4 
        WHEN e.grade = 'B' THEN 3 
        WHEN e.grade = 'C' THEN 2 
        WHEN e.grade = 'D' THEN 1 
        WHEN e.grade = 'F' THEN 0 
      END) AS gpa
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY gpa DESC
LIMIT 1;
```

## Bonus Challenge (if you have time remaining)

Create a view that shows a comprehensive student report including:
- Student name
- Major
- Total credits earned
- Overall GPA
- Number of courses taken
- List of courses (as a concatenated string)

```sql
CREATE VIEW student_report AS
SELECT 
    s.student_id,
    CONCAT(s.first_name, ' ', s.last_name) AS student_name,
    s.major,
    s.credits_earned,
    AVG(CASE 
          WHEN e.grade = 'A' THEN 4 
          WHEN e.grade = 'B' THEN 3 
          WHEN e.grade = 'C' THEN 2 
          WHEN e.grade = 'D' THEN 1 
          WHEN e.grade = 'F' THEN 0 
        END) AS overall_gpa,
    COUNT(e.enrollment_id) AS courses_taken,
    GROUP_CONCAT(c.title SEPARATOR ', ') AS course_list
FROM 
    students s
JOIN 
    enrollments e ON s.student_id = e.student_id
JOIN 
    courses c ON e.course_id = c.course_id
GROUP BY 
    s.student_id, student_name, s.major, s.credits_earned;

-- View the report
SELECT * FROM student_report;
```

This challenge will help you practice creating databases, defining relationships between tables, inserting data, and writing various types of queries—all essential skills for working with MySQL databases.

## Clean Up (Optional)

If you want to remove the database after completing the challenge:

```sql
DROP DATABASE university;
```
