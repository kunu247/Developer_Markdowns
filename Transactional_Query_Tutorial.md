Using transaction control in a stored procedure that handles **SELECT**, **INSERT**, **UPDATE**, and **DELETE** operations ensures that all the queries are treated as a single unit of work. If any of the operations fail, the entire transaction is rolled back, maintaining data integrity.

Below is a full example of how to implement transaction control in a stored procedure that performs **SELECT**, **INSERT**, **UPDATE**, and **DELETE** operations with proper error handling using `TRY...CATCH`.

### Example Stored Procedure

```sql
CREATE PROCEDURE usp_ManageRecords
    @Operation NVARCHAR(10),   -- The operation to perform: 'SELECT', 'INSERT', 'UPDATE', 'DELETE'
    @Id INT = NULL,            -- Record ID (for SELECT/UPDATE/DELETE)
    @NewValue NVARCHAR(100) = NULL  -- New value (for INSERT/UPDATE)
AS
BEGIN
    -- Set isolation level to READ COMMITTED (can be changed to READ UNCOMMITTED for dirty reads)
    SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

    -- Start transaction
    BEGIN TRANSACTION;

    -- Try-Catch block for error handling
    BEGIN TRY
        -- SELECT operation
        IF @Operation = 'SELECT'
        BEGIN
            SELECT *
            FROM Records
            WHERE Id = @Id;
        END

        -- INSERT operation
        ELSE IF @Operation = 'INSERT'
        BEGIN
            INSERT INTO Records (Value)
            VALUES (@NewValue);

            -- Output the new inserted record ID (if required)
            SELECT SCOPE_IDENTITY() AS NewRecordId;
        END

        -- UPDATE operation
        ELSE IF @Operation = 'UPDATE'
        BEGIN
            UPDATE Records
            SET Value = @NewValue
            WHERE Id = @Id;
        END

        -- DELETE operation
        ELSE IF @Operation = 'DELETE'
        BEGIN
            DELETE FROM Records
            WHERE Id = @Id;
        END

        -- If no errors, commit the transaction
        COMMIT TRANSACTION;
    END TRY

    -- CATCH block for error handling
    BEGIN CATCH
        -- Rollback transaction in case of error
        ROLLBACK TRANSACTION;

        -- Capture and return error details
        DECLARE @ErrorMessage NVARCHAR(4000), @ErrorSeverity INT, @ErrorState INT;
        SELECT @ErrorMessage = ERROR_MESSAGE(), 
               @ErrorSeverity = ERROR_SEVERITY(), 
               @ErrorState = ERROR_STATE();

        -- Output the error message
        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH;
END;
GO
```

### Explanation of the Stored Procedure:

1. **Input Parameters**:
   - `@Operation`: Specifies the type of operation to perform (`SELECT`, `INSERT`, `UPDATE`, or `DELETE`).
   - `@Id`: The record identifier for operations that need it (`SELECT`, `UPDATE`, or `DELETE`).
   - `@NewValue`: The value to be inserted or updated in the `Records` table.

2. **Transaction Control**:
   - **`BEGIN TRANSACTION`** starts a new transaction, meaning all queries within the transaction will either succeed together or fail together.
   - The `TRY...CATCH` block ensures that if any error occurs, the transaction will be rolled back.

3. **`TRY` Block**:
   - Based on the value of `@Operation`, it decides whether to perform a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` operation.
   - If the operation succeeds, the transaction is **committed** using `COMMIT TRANSACTION`.

4. **`CATCH` Block**:
   - If any error occurs during the transaction, control is passed to the `CATCH` block.
   - In the `CATCH` block, the transaction is **rolled back** using `ROLLBACK TRANSACTION`.
   - The error message is captured using `ERROR_MESSAGE()`, `ERROR_SEVERITY()`, and `ERROR_STATE()`, and re-raised with `RAISERROR` to return the error to the caller.

### Usage:

1. **SELECT Operation**:
   ```sql
   EXEC usp_ManageRecords @Operation = 'SELECT', @Id = 1;
   ```

2. **INSERT Operation**:
   ```sql
   EXEC usp_ManageRecords @Operation = 'INSERT', @NewValue = 'New Record Value';
   ```

3. **UPDATE Operation**:
   ```sql
   EXEC usp_ManageRecords @Operation = 'UPDATE', @Id = 1, @NewValue = 'Updated Value';
   ```

4. **DELETE Operation**:
   ```sql
   EXEC usp_ManageRecords @Operation = 'DELETE', @Id = 1;
   ```

### Considerations:
- **Error Handling**: If an error occurs at any stage (for example, trying to update a non-existent record), the transaction will be rolled back, ensuring no partial changes are committed.
- **Performance**: Using a `TRY...CATCH` block with transactions is essential to maintain data integrity in case of errors, though it introduces some overhead.
- **Concurrency**: The transaction isolation level is set to `READ COMMITTED` by default, which ensures that only committed data is read. For more performance, you could change it to `READ UNCOMMITTED`, but that introduces the risk of dirty reads.
