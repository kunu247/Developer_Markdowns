To create a BCA Students Attendance System using SQLite, we'll first define the necessary tables and then provide the full CRUD (Create, Read, Update, Delete) operations. Let's assume we have the following two tables: `Students` and `Attendance`.

### 1. Creating Tables

#### Students Table
The `Students` table will store information about each student.

```sql
CREATE TABLE Students (
    StudentId INTEGER PRIMARY KEY AUTOINCREMENT,
    Name TEXT NOT NULL,
    EnrollmentNumber TEXT UNIQUE NOT NULL,
    Email TEXT,
    Course TEXT,
    Semester INTEGER
);
```

#### Attendance Table
The `Attendance` table will store attendance records.

```sql
CREATE TABLE Attendance (
    AttendanceId INTEGER PRIMARY KEY AUTOINCREMENT,
    StudentId INTEGER NOT NULL,
    AttendanceDate DATE NOT NULL,
    Status TEXT CHECK(Status IN ('Present', 'Absent', 'Late', 'Excused')),
    FOREIGN KEY (StudentId) REFERENCES Students(StudentId)
);
```

### 2. CRUD Operations

#### a. Create Operations

- **Insert a New Student**

```sql
INSERT INTO Students (Name, EnrollmentNumber, Email, Course, Semester) 
VALUES ('John Doe', 'BCA2023001', 'john.doe@example.com', 'BCA', 1);
```

- **Insert a New Attendance Record**

```sql
INSERT INTO Attendance (StudentId, AttendanceDate, Status) 
VALUES (1, '2024-09-01', 'Present');
```

#### b. Read Operations

- **Select All Students**

```sql
SELECT * FROM Students;
```

- **Select All Attendance Records**

```sql
SELECT * FROM Attendance;
```

- **Select Attendance for a Specific Student**

```sql
SELECT * FROM Attendance WHERE StudentId = 1;
```

#### c. Update Operations

- **Update Student Information**

```sql
UPDATE Students 
SET Email = 'john.newemail@example.com', Semester = 2 
WHERE StudentId = 1;
```

- **Update Attendance Status**

```sql
UPDATE Attendance 
SET Status = 'Absent' 
WHERE AttendanceId = 1;
```

#### d. Delete Operations

- **Delete a Student Record**

```sql
DELETE FROM Students WHERE StudentId = 1;
```

- **Delete an Attendance Record**

```sql
DELETE FROM Attendance WHERE AttendanceId = 1;
```

### 3. Additional Notes

- Ensure you handle exceptions properly in your application to manage any constraint violations or other errors.
- You may want to add more features like checking for duplicates or ensuring data integrity before inserting or updating records.

These queries will help you manage the Students and Attendance records effectively in your BCA Students Attendance System using SQLite.