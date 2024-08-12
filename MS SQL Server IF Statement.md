The `IF...ELSE` statement in MS SQL Server is used to execute a block of code if a condition is true. If the condition is false, another block of code can be executed using the `ELSE` clause. Below are detailed examples covering various scenarios:

### 1. **Basic `IF...ELSE` Statement**
   This example checks a simple condition.

   ```sql
   DECLARE @Age INT = 18;

   IF @Age >= 18
   BEGIN
       PRINT 'You are eligible to vote.';
   END
   ELSE
   BEGIN
       PRINT 'You are not eligible to vote.';
   END
   ```

### 2. **`IF...ELSE IF...ELSE` Statement**
   This example demonstrates checking multiple conditions using `ELSE IF`.

   ```sql
   DECLARE @Score INT = 85;

   IF @Score >= 90
   BEGIN
       PRINT 'Grade A';
   END
   ELSE IF @Score >= 80
   BEGIN
       PRINT 'Grade B';
   END
   ELSE IF @Score >= 70
   BEGIN
       PRINT 'Grade C';
   END
   ELSE
   BEGIN
       PRINT 'Grade F';
   END
   ```

### 3. **`IF` Statement Without `ELSE`**
   If you don't need an alternative action for a false condition, you can omit the `ELSE` clause.

   ```sql
   DECLARE @IsActive BIT = 1;

   IF @IsActive = 1
   BEGIN
       PRINT 'The account is active.';
   END
   ```

### 4. **Nested `IF...ELSE` Statement**
   You can nest `IF...ELSE` statements within each other.

   ```sql
   DECLARE @Temperature INT = 32;

   IF @Temperature > 30
   BEGIN
       PRINT 'It is hot outside.';

       IF @Temperature > 40
       BEGIN
           PRINT 'It is extremely hot!';
       END
       ELSE
       BEGIN
           PRINT 'It is moderately hot.';
       END
   END
   ELSE
   BEGIN
       PRINT 'The weather is cool.';
   END
   ```

### 5. **`IF EXISTS` Statement**
   You can use `IF EXISTS` to check for the existence of rows in a table.

   ```sql
   IF EXISTS (SELECT 1 FROM Students WHERE Age > 18)
   BEGIN
       PRINT 'There are students older than 18.';
   END
   ELSE
   BEGIN
       PRINT 'No students are older than 18.';
   END
   ```

### 6. **`IF NOT EXISTS` Statement**
   The `IF NOT EXISTS` statement checks for the absence of rows.

   ```sql
   IF NOT EXISTS (SELECT 1 FROM Students WHERE Age > 18)
   BEGIN
       PRINT 'No students are older than 18.';
   END
   ELSE
   BEGIN
       PRINT 'There are students older than 18.';
   END
   ```

### 7. **`IF` with `BEGIN...END` Blocks**
   It's good practice to use `BEGIN...END` even for a single statement within `IF` or `ELSE` to avoid errors.

   ```sql
   DECLARE @Status VARCHAR(10) = 'Active';

   IF @Status = 'Active'
   BEGIN
       UPDATE Students SET Status = 'Inactive' WHERE Status = 'Active';
   END
   ELSE
   BEGIN
       PRINT 'No active students to update.';
   END
   ```

### 8. **Using `IF` in Stored Procedures**
   You can use `IF...ELSE` statements within stored procedures to control flow based on parameters or data.

   ```sql
   CREATE PROCEDURE CheckStudentAge
   @StudentId INT
   AS
   BEGIN
       DECLARE @Age INT;

       SELECT @Age = Age FROM Students WHERE Id = @StudentId;

       IF @Age >= 18
       BEGIN
           PRINT 'Student is an adult.';
       END
       ELSE
       BEGIN
           PRINT 'Student is a minor.';
       END
   END
   ```

### 9. **`IF` with `RETURN` Statement**
   You can use `RETURN` to exit a stored procedure or function early.

   ```sql
   CREATE PROCEDURE ValidateAge
   @Age INT
   AS
   BEGIN
       IF @Age < 0
       BEGIN
           PRINT 'Invalid age.';
           RETURN;
       END

       IF @Age >= 18
       BEGIN
           PRINT 'Adult';
       END
       ELSE
       BEGIN
           PRINT 'Minor';
       END
   END
   ```

### 10. **Handling Errors with `IF...ELSE`**
You can use `TRY...CATCH` blocks with `IF...ELSE` to handle errors.

```sql
    BEGIN TRY
        DECLARE @Result INT;

        IF @Result IS NULL
        BEGIN
            THROW 50000, 'Result cannot be NULL', 1;
        END
        ELSE
        BEGIN
            PRINT 'Result is valid.';
        END
    END TRY
    BEGIN CATCH
        PRINT 'An error occurred: ' + ERROR_MESSAGE();
    END CATCH
```

These examples cover a range of use cases for `IF...ELSE` statements in MS SQL Server, from simple conditions to complex logic involving error handling and data validation.