The `EXEC` (or `EXECUTE`) statement in MS SQL Server is used to execute a stored procedure, a string of dynamic SQL, or an extended stored procedure. It is a versatile command that allows for the execution of SQL code at runtime. Below are detailed examples covering various use cases of the `EXEC` statement:

### 1. **Executing a Stored Procedure**
   The `EXEC` statement is commonly used to run stored procedures.

   **Example:**
   ```sql
   -- Assuming you have a stored procedure named 'usp_GetStudentById'
   EXEC usp_GetStudentById @StudentId = 1;
   ```

   Here, `usp_GetStudentById` is the name of the stored procedure, and `@StudentId` is a parameter passed to it.

### 2. **Executing a Stored Procedure Without Parameters**
   If a stored procedure does not require parameters, you can simply execute it without any arguments.

   **Example:**
   ```sql
   EXEC usp_GetAllStudents;
   ```

   This will run the `usp_GetAllStudents` stored procedure, which might return a list of students.

### 3. **Executing a Stored Procedure with Output Parameters**
   A stored procedure can return values through output parameters.

   **Example:**
   ```sql
   DECLARE @StudentName NVARCHAR(100);

   EXEC usp_GetStudentNameById @StudentId = 1, @StudentName = @StudentName OUTPUT;

   PRINT @StudentName;
   ```

   In this example, `@StudentName` is an output parameter that gets populated with the student's name after executing the stored procedure.

### 4. **Executing Dynamic SQL**
   `EXEC` can be used to execute dynamic SQL, which is SQL code generated at runtime.

   **Example:**
   ```sql
   DECLARE @SQL NVARCHAR(MAX);

   SET @SQL = 'SELECT * FROM Students WHERE Age > 18';

   EXEC sp_executesql @SQL;
   ```

   This example dynamically creates an SQL query to select students older than 18 and executes it using `sp_executesql`.

### 5. **Using `sp_executesql` with Parameters**
   `sp_executesql` allows for the execution of dynamic SQL with parameters, which is more secure and efficient than concatenating strings.

   **Example:**
   ```sql
   DECLARE @SQL NVARCHAR(MAX);
   DECLARE @MinAge INT = 18;

   SET @SQL = 'SELECT * FROM Students WHERE Age > @MinAge';

   EXEC sp_executesql @SQL, N'@MinAge INT', @MinAge = @MinAge;
   ```

   This executes a dynamic SQL query with a parameter, making it safer against SQL injection.

### 6. **Executing a String of SQL Code**
   You can use `EXEC` to execute any string of SQL code.

   **Example:**
   ```sql
   EXEC('SELECT TOP 10 * FROM Students');
   ```

   This runs a simple query to select the top 10 records from the `Students` table.

### 7. **Executing Extended Stored Procedures**
   Extended stored procedures are custom routines written in external programming languages like C. These can also be executed using the `EXEC` statement.

   **Example:**
   ```sql
   EXEC xp_cmdshell 'dir';
   ```

   This executes the `xp_cmdshell` extended stored procedure, which runs a command prompt command (`dir` in this case).

### 8. **Executing Stored Procedure with Multiple Parameters**
   When a stored procedure requires multiple parameters, you can pass them all in the `EXEC` statement.

   **Example:**
   ```sql
   EXEC usp_UpdateStudentInfo @StudentId = 1, @Name = 'John Doe', @Age = 22;
   ```

   This executes the `usp_UpdateStudentInfo` stored procedure with three parameters: `@StudentId`, `@Name`, and `@Age`.

### 9. **Executing Stored Procedures in Different Schemas**
   If your stored procedure belongs to a different schema, specify the schema name before the procedure name.

   **Example:**
   ```sql
   EXEC [SchemaName].usp_ProcedureName @Parameter = Value;
   ```

   For example:
   ```sql
   EXEC dbo.usp_GetStudentById @StudentId = 1;
   ```

### 10. **Executing a Stored Procedure with Default Parameters**
    If a stored procedure has default values for parameters, you can omit those parameters in the `EXEC` statement.

    **Example:**
    ```sql
    -- Assuming 'usp_GetStudentInfo' has a default value for '@Age'
    EXEC usp_GetStudentInfo @StudentId = 1;
    ```

    If `@Age` has a default value, it will be used automatically.

### 11. **Executing a Stored Procedure and Handling Errors**
    You can use `TRY...CATCH` to handle errors when executing a stored procedure.

    **Example:**
    ```sql
    BEGIN TRY
        EXEC usp_UpdateStudentInfo @StudentId = 1, @Name = 'John Doe', @Age = 22;
    END TRY
    BEGIN CATCH
        PRINT 'Error: ' + ERROR_MESSAGE();
    END CATCH
    ```

    This example attempts to execute `usp_UpdateStudentInfo` and prints an error message if an exception occurs.

### 12. **Executing Stored Procedures with Named Parameters**
    You can specify parameters by name, which makes the code clearer and avoids confusion with the order of parameters.

    **Example:**
    ```sql
    EXEC usp_UpdateStudentInfo @Name = 'John Doe', @Age = 22, @StudentId = 1;
    ```

    Here, parameters are passed in a different order than defined, but they are correctly matched by name.

### 13. **Executing Dynamic SQL in a Different Context**
    You can execute dynamic SQL in the context of a different database or user.

    **Example:**
    ```sql
    EXEC('USE AdventureWorks; SELECT * FROM Person.Person');
    ```

    This changes the database context to `AdventureWorks` and selects data from the `Person` table.

### 14. **Executing System Stored Procedures**

System stored procedures are built-in procedures that perform administrative tasks.

**Example:**

	```sql
    EXEC sp_help 'Students';
    ```

This runs the `sp_help` system stored procedure, which provides information about the `Students` table.

### 15. **Passing NULL Values to Parameters**
If you need to pass a `NULL` value to a parameter, you can do so explicitly.

    **Example:**
    ```sql
    EXEC usp_UpdateStudentInfo @StudentId = 1, @Name = NULL, @Age = 22;
    ```


This passes a `NULL` value for the `@Name` parameter.

These examples provide a comprehensive overview of the `EXEC` statement's capabilities in MS SQL Server, showcasing its flexibility in executing stored procedures, dynamic SQL, and extended stored procedures.