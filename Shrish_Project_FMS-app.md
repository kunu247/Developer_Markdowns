App Name: Mumbai ka Vyapari
Database: MS SQL Server
Programming Languages Used: C#(.NET F/w 4.8.1)

Suppose we have one food stall, There is
a) Need to manage all the Customers
b) Need to manage all the food types or food(Vadapav, bhaji paav, Idli etc.)
c) Need to manage all the co-workers
d) Need to manage Ingredients which is used in this stall
e) Need to manage all the Incomes and Expenditures
f) Calculate and Show Daily, Monthly, Yearly Sales and Purchase which is belong to  this Stall

Generate or create the MS SQL Table structures and Stored Procedures which is used in Windows Form App

### To create the MS SQL Server table structures and stored procedures for the "Mumbai ka Vyapari" application, we'll define tables for customers, food types, co-workers, ingredients, incomes, and expenditures. We will also create stored procedures for CRUD operations and for calculating daily, monthly, and yearly sales and purchases.

<h2 style="color: yellow; font-weight: 600">SQL Table Structures<h2>

---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select CustomerID if isActive value is true By cName (Flag = N) or cPhone (Flag = P) or cEmail (Flag = E) or cAddress (Flag = A) or cRegistrationDate (Flag = R),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:

```sql
CREATE TABLE Customers (
       Id INT IDENTITY(1,1) PRIMARY KEY,
       CustomerID NVARCHAR(10) UNIQUE NOT NULL,
       cName NVARCHAR(100) NOT NULL,
       cPhone NVARCHAR(15),
       cEmail NVARCHAR(100),
       cAddress NVARCHAR(255),
       cRegistrationDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

To manage the `Customers` table using stored procedures with transactional support and error handling in SQL Server, here are the required stored procedures for selecting, inserting, updating, and deleting records:

1. <h3 style="color: tomato; font-weight: 600">Customers Table</h3>

```sql
CREATE TABLE Customers (
   Id INT IDENTITY(1,1) PRIMARY KEY,
   CustomerID NVARCHAR(10) UNIQUE NOT NULL,
   cName NVARCHAR(100) NOT NULL,
   cPhone NVARCHAR(15),
   cEmail NVARCHAR(100),
   cAddress NVARCHAR(255),
   cRegistrationDate DATETIME DEFAULT GETDATE(),
   CreatedBy NVARCHAR(100) NULL,
   CreatedOn DATETIME DEFAULT GETDATE(),
   UpdatedBy NVARCHAR(100) NULL,
   UpdatedOn DATETIME NULL,
   isActive BIT DEFAULT 1 -- Add the isActive column here
);
```

---

Here are the SQL stored procedures for the `Customers` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllCustomers
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Customers;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectCustomerById
    @Id INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Customers WHERE Id = @Id;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select CustomerID if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectCustomerIDByFlag
    @Flag CHAR(1), -- 'N' for cName, 'P' for cPhone, 'E' for cEmail, 'A' for cAddress, 'R' for cRegistrationDate
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT CustomerID FROM Customers WHERE isActive = 1 AND ';

    IF @Flag = 'N'
        SET @SQL += 'cName = @Value';
    ELSE IF @Flag = 'P'
        SET @SQL += 'cPhone = @Value';
    ELSE IF @Flag = 'E'
        SET @SQL += 'cEmail = @Value';
    ELSE IF @Flag = 'A'
        SET @SQL += 'cAddress = @Value';
    ELSE IF @Flag = 'R'
        SET @SQL += 'cRegistrationDate = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertCustomer
    @CustomerID NVARCHAR(10),
    @cName NVARCHAR(100),
    @cPhone NVARCHAR(15),
    @cEmail NVARCHAR(100),
    @cAddress NVARCHAR(255),
    @cRegistrationDate DATETIME = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Customers (CustomerID, cName, cPhone, cEmail, cAddress, cRegistrationDate, CreatedBy)
        VALUES (@CustomerID, @cName, @cPhone, @cEmail, @cAddress, 
                COALESCE(@cRegistrationDate, GETDATE()), @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateCustomer
    @Id INT,
    @cName NVARCHAR(100),
    @cPhone NVARCHAR(15),
    @cEmail NVARCHAR(100),
    @cAddress NVARCHAR(255),
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Customers
        SET 
            cName = @cName,
            cPhone = @cPhone,
            cEmail = @cEmail,
            cAddress = @cAddress,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE Id = @Id;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteCustomer
    @Id INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Customers WHERE Id = @Id;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures will handle basic CRUD operations for the `Customers` table while ensuring proper error handling and transaction management.

---
---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select FoodTypeID if isActive value is true By fName (Flag = N) or fDescription (Flag = D) or fPrice (Flag = P),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:

```sql
   CREATE TABLE FoodTypes (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       FoodTypeID NVARCHAR(10) UNIQUE NOT NULL,
       fName NVARCHAR(100) NOT NULL,
       fDescription NVARCHAR(255),
       fPrice DECIMAL(10, 2) NOT NULL,
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

2. <h3 style="color: tomato; font-weight: 600">FoodTypes Table</h3>

```sql
   CREATE TABLE FoodTypes (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       FoodTypeID NVARCHAR(10) UNIQUE NOT NULL,
       fName NVARCHAR(100) NOT NULL,
       fDescription NVARCHAR(255),
       fPrice DECIMAL(10, 2) NOT NULL,
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

---

Here are the SQL stored procedures for the `FoodTypes` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllFoodTypes
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM FoodTypes;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectFoodTypeById
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM FoodTypes WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select `FoodTypeID` if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectFoodTypeIDByFlag
    @Flag CHAR(1), -- 'N' for fName, 'D' for fDescription, 'P' for fPrice
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT FoodTypeID FROM FoodTypes WHERE isActive = 1 AND ';

    IF @Flag = 'N'
        SET @SQL += 'fName = @Value';
    ELSE IF @Flag = 'D'
        SET @SQL += 'fDescription = @Value';
    ELSE IF @Flag = 'P'
        SET @SQL += 'fPrice = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertFoodType
    @FoodTypeID NVARCHAR(10),
    @fName NVARCHAR(100),
    @fDescription NVARCHAR(255),
    @fPrice DECIMAL(10, 2),
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO FoodTypes (FoodTypeID, fName, fDescription, fPrice, CreatedBy)
        VALUES (@FoodTypeID, @fName, @fDescription, @fPrice, @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateFoodType
    @ID INT,
    @fName NVARCHAR(100),
    @fDescription NVARCHAR(255),
    @fPrice DECIMAL(10, 2),
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE FoodTypes
        SET 
            fName = @fName,
            fDescription = @fDescription,
            fPrice = @fPrice,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteFoodType
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM FoodTypes WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures provide comprehensive CRUD operations for the `FoodTypes` table, ensuring robust error handling and transaction management.

---
---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select CoWorkerID if isActive value is true By cwName (Flag = N) or cwPhone (Flag = P) or cwAddress (Flag = A) or cwHireDate (Flag = H),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:

3. <h3 style="color: tomato; font-weight: 600">CoWorkers Table</h3>
```sql
CREATE TABLE CoWorkers (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       CoWorkerID NVARCHAR(10) UNIQUE NOT NULL,
       cwName NVARCHAR(100) NOT NULL,
       cwRole NVARCHAR(50),
       cwPhone NVARCHAR(15),
       cwAddress NVARCHAR(255),
       cwHireDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

---

Here are the SQL stored procedures for the `CoWorkers` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllCoWorkers
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM CoWorkers;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectCoWorkerById
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM CoWorkers WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select `CoWorkerID` if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectCoWorkerIDByFlag
    @Flag CHAR(1), -- 'N' for cwName, 'P' for cwPhone, 'A' for cwAddress, 'H' for cwHireDate
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT CoWorkerID FROM CoWorkers WHERE isActive = 1 AND ';

    IF @Flag = 'N'
        SET @SQL += 'cwName = @Value';
    ELSE IF @Flag = 'P'
        SET @SQL += 'cwPhone = @Value';
    ELSE IF @Flag = 'A'
        SET @SQL += 'cwAddress = @Value';
    ELSE IF @Flag = 'H'
        SET @SQL += 'cwHireDate = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertCoWorker
    @CoWorkerID NVARCHAR(10),
    @cwName NVARCHAR(100),
    @cwRole NVARCHAR(50) = NULL,
    @cwPhone NVARCHAR(15) = NULL,
    @cwAddress NVARCHAR(255) = NULL,
    @cwHireDate DATETIME = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO CoWorkers (CoWorkerID, cwName, cwRole, cwPhone, cwAddress, cwHireDate, CreatedBy)
        VALUES (@CoWorkerID, @cwName, @cwRole, @cwPhone, @cwAddress, 
                COALESCE(@cwHireDate, GETDATE()), @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateCoWorker
    @ID INT,
    @cwName NVARCHAR(100),
    @cwRole NVARCHAR(50) = NULL,
    @cwPhone NVARCHAR(15) = NULL,
    @cwAddress NVARCHAR(255) = NULL,
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE CoWorkers
        SET 
            cwName = @cwName,
            cwRole = @cwRole,
            cwPhone = @cwPhone,
            cwAddress = @cwAddress,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteCoWorker
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM CoWorkers WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures will handle basic CRUD operations for the `CoWorkers` table, ensuring proper error handling and transaction management.

---
---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select IID if isActive value is true By
	iName (Flag = N) or
	iQuantity (Flag = Q) or
	iUnitPrice (Flag = P) or
	iPurchaseDate (Flag = D),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:
   CREATE TABLE Ingredients (
       ID INT IDENTITY(1,1) PRIMARY KEY,
	   IID VARCHAR(10) UNIQUE NOT NULL,
       iName NVARCHAR(100) NOT NULL,
       iQuantity NVARCHAR(50),
       iUOM NVARCHAR(20) NOT NULL,
       iUnitPrice DECIMAL(10, 2) NOT NULL,
       iPurchaseDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
   
1. <h3 style="color: tomato; font-weight: 600">Ingredients Table</h3>
```sql
   CREATE TABLE Ingredients (
       ID INT IDENTITY(1,1) PRIMARY KEY,
	   IID VARCHAR(10) UNIQUE NOT NULL,
       iName NVARCHAR(100) NOT NULL,
       iQuantity NVARCHAR(50),
       iUOM NVARCHAR(20) NOT NULL,
       iUnitPrice DECIMAL(10, 2) NOT NULL,
       iPurchaseDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

---

Here are the SQL stored procedures for the `Ingredients` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllIngredients
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Ingredients;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectIngredientById
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Ingredients WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select `IID` if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectIIDByFlag
    @Flag CHAR(1), -- 'N' for iName, 'Q' for iQuantity, 'P' for iUnitPrice, 'D' for iPurchaseDate
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT IID FROM Ingredients WHERE isActive = 1 AND ';

    IF @Flag = 'N'
        SET @SQL += 'iName = @Value';
    ELSE IF @Flag = 'Q'
        SET @SQL += 'iQuantity = @Value';
    ELSE IF @Flag = 'P'
        SET @SQL += 'iUnitPrice = @Value';
    ELSE IF @Flag = 'D'
        SET @SQL += 'iPurchaseDate = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertIngredient
    @IID VARCHAR(10),
    @iName NVARCHAR(100),
    @iQuantity NVARCHAR(50) = NULL,
    @iUOM NVARCHAR(20),
    @iUnitPrice DECIMAL(10, 2),
    @iPurchaseDate DATETIME = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Ingredients (IID, iName, iQuantity, iUOM, iUnitPrice, iPurchaseDate, CreatedBy)
        VALUES (@IID, @iName, @iQuantity, @iUOM, @iUnitPrice, 
                COALESCE(@iPurchaseDate, GETDATE()), @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateIngredient
    @ID INT,
    @iName NVARCHAR(100),
    @iQuantity NVARCHAR(50) = NULL,
    @iUOM NVARCHAR(20),
    @iUnitPrice DECIMAL(10, 2),
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Ingredients
        SET 
            iName = @iName,
            iQuantity = @iQuantity,
            iUOM = @iUOM,
            iUnitPrice = @iUnitPrice,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteIngredient
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Ingredients WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures provide comprehensive CRUD operations for the `Ingredients` table, ensuring robust error handling and transaction management.

---
---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select ID if isActive value is true By
	CustomerID (Flag = C) or
	FoodTypeID (Flag = F) or
	IncomeDate (Flag = D),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:
CREATE TABLE Incomes (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
       FoodTypeID INT FOREIGN KEY REFERENCES FoodTypes(FoodTypeID),
       Quantity INT NOT NULL,
       TotalAmount DECIMAL(10, 2) NOT NULL,
       IncomeDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );

5. <h3 style="color: tomato; font-weight: 600">Incomes Table</h3>
```sql
CREATE TABLE Incomes (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
       FoodTypeID INT FOREIGN KEY REFERENCES FoodTypes(FoodTypeID),
       Quantity INT NOT NULL,
       TotalAmount DECIMAL(10, 2) NOT NULL,
       IncomeDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here
   );
```

Here are the SQL stored procedures for the `Incomes` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllIncomes
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Incomes;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectIncomeById
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Incomes WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select `ID` if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectIncomeIDByFlag
    @Flag CHAR(1), -- 'C' for CustomerID, 'F' for FoodTypeID, 'D' for IncomeDate
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT ID FROM Incomes WHERE isActive = 1 AND ';

    IF @Flag = 'C'
        SET @SQL += 'CustomerID = @Value';
    ELSE IF @Flag = 'F'
        SET @SQL += 'FoodTypeID = @Value';
    ELSE IF @Flag = 'D'
        SET @SQL += 'IncomeDate = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertIncome
    @CustomerID INT,
    @FoodTypeID INT,
    @Quantity INT,
    @TotalAmount DECIMAL(10, 2),
    @IncomeDate DATETIME = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Incomes (CustomerID, FoodTypeID, Quantity, TotalAmount, IncomeDate, CreatedBy)
        VALUES (@CustomerID, @FoodTypeID, @Quantity, @TotalAmount, 
                COALESCE(@IncomeDate, GETDATE()), @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateIncome
    @ID INT,
    @CustomerID INT,
    @FoodTypeID INT,
    @Quantity INT,
    @TotalAmount DECIMAL(10, 2),
    @IncomeDate DATETIME,
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Incomes
        SET 
            CustomerID = @CustomerID,
            FoodTypeID = @FoodTypeID,
            Quantity = @Quantity,
            TotalAmount = @TotalAmount,
            IncomeDate = @IncomeDate,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteIncome
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Incomes WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures provide comprehensive CRUD operations for the `Incomes` table, ensuring robust error handling and transaction management.

---
---

Generate an MS SQL 
Select All Record,
Select Single record By ID,
Select ID if isActive value is true By
	IngredientID (Flag = I) or
	eTotalCost (Flag = C) or
	ExpenditureDate (Flag = D),
Insert New Record,
Update Record,
Delete Record etc. Stored Procedure Query for the following Create Table Query with Try-Catch and Transactional statements:
   CREATE TABLE Expenditures (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       IngredientID INT FOREIGN KEY REFERENCES Ingredients(IID),
       eQuantity INT NOT NULL,
       eTotalCost DECIMAL(10, 2) NOT NULL,
       ExpenditureDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here   
   );

6. <h3 style="color: tomato; font-weight: 600">Expenditures Table</h3>
```sql
   CREATE TABLE Expenditures (
       ID INT IDENTITY(1,1) PRIMARY KEY,
       IngredientID INT FOREIGN KEY REFERENCES Ingredients(IID),
       eQuantity INT NOT NULL,
       eTotalCost DECIMAL(10, 2) NOT NULL,
       ExpenditureDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL,
	   isActive BIT DEFAULT 1 -- Add the isActive column here   
   );
```

Here are the SQL stored procedures for the `Expenditures` table. These procedures include `SELECT`, `INSERT`, `UPDATE`, and `DELETE` operations with proper `TRY-CATCH` and `TRANSACTION` handling.

### 1. Select All Records

```sql
CREATE PROCEDURE SelectAllExpenditures
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Expenditures;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 2. Select Single Record By ID

```sql
CREATE PROCEDURE SelectExpenditureById
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Expenditures WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 3. Select `ID` if `isActive` is `true` By Flag and Value

```sql
CREATE PROCEDURE SelectExpenditureIDByFlag
    @Flag CHAR(1), -- 'I' for IngredientID, 'C' for eTotalCost, 'D' for ExpenditureDate
    @Value NVARCHAR(255)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @SQL NVARCHAR(MAX);

    SET @SQL = 'SELECT ID FROM Expenditures WHERE isActive = 1 AND ';

    IF @Flag = 'I'
        SET @SQL += 'IngredientID = @Value';
    ELSE IF @Flag = 'C'
        SET @SQL += 'eTotalCost = @Value';
    ELSE IF @Flag = 'D'
        SET @SQL += 'ExpenditureDate = @Value';

    BEGIN TRY
        EXEC sp_executesql @SQL, N'@Value NVARCHAR(255)', @Value;
    END TRY
    BEGIN CATCH
        -- Error Handling
        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 4. Insert New Record

```sql
CREATE PROCEDURE InsertExpenditure
    @IngredientID INT,
    @eQuantity INT,
    @eTotalCost DECIMAL(10, 2),
    @ExpenditureDate DATETIME = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Expenditures (IngredientID, eQuantity, eTotalCost, ExpenditureDate, CreatedBy)
        VALUES (@IngredientID, @eQuantity, @eTotalCost, 
                COALESCE(@ExpenditureDate, GETDATE()), @CreatedBy);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 5. Update Record

```sql
CREATE PROCEDURE UpdateExpenditure
    @ID INT,
    @IngredientID INT,
    @eQuantity INT,
    @eTotalCost DECIMAL(10, 2),
    @ExpenditureDate DATETIME,
    @UpdatedBy NVARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Expenditures
        SET 
            IngredientID = @IngredientID,
            eQuantity = @eQuantity,
            eTotalCost = @eTotalCost,
            ExpenditureDate = @ExpenditureDate,
            UpdatedBy = @UpdatedBy,
            UpdatedOn = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

### 6. Delete Record

```sql
CREATE PROCEDURE DeleteExpenditure
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Expenditures WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Error Handling
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        SELECT 
            ERROR_MESSAGE() AS ErrorMessage,
            ERROR_SEVERITY() AS ErrorSeverity,
            ERROR_STATE() AS ErrorState;
    END CATCH
END;
```

These stored procedures provide comprehensive CRUD operations for the `Expenditures` table, ensuring robust error handling and transaction management.

---


### Stored Procedures
1. <h3 style="color: tomato; font-weight: 600">Insert Customer</h3>
To create an `INSERT` stored procedure for the `Customers` table, you can use a `TRY-CATCH` block along with `TRANSACTION` statements to handle errors and ensure data integrity. The stored procedure will insert a new customer record into the `Customers` table, and if an error occurs, it will roll back the transaction.

Here is the stored procedure query:

```sql
CREATE PROCEDURE InsertCustomer
    @cName NVARCHAR(100),
    @cPhone NVARCHAR(15) = NULL,
    @cEmail NVARCHAR(100) = NULL,
    @cAddress NVARCHAR(255) = NULL,
    @CreatedBy NVARCHAR(100) = NULL
AS
BEGIN
    -- Start transaction
    BEGIN TRANSACTION;

    BEGIN TRY
        -- Insert statement
        INSERT INTO Customers (cName, cPhone, cEmail, cAddress, CreatedBy, CreatedOn)
        VALUES (@cName, @cPhone, @cEmail, @cAddress, @CreatedBy, GETDATE());

        -- Commit transaction if no errors
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- Rollback transaction in case of error
        ROLLBACK TRANSACTION;

        -- Return error information
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT @ErrorMessage = ERROR_MESSAGE(),
               @ErrorSeverity = ERROR_SEVERITY(),
               @ErrorState = ERROR_STATE();

        -- Throw the error to the calling procedure or batch
        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

### Key Components of the Stored Procedure

1. **Parameters**: The procedure accepts input parameters corresponding to the table columns except for `CustomerID`, `cRegistrationDate`, `CreatedOn`, and `UpdatedOn`, which are handled automatically.

2. **Transaction Management**:
   - `BEGIN TRANSACTION;` starts a new transaction.
   - `COMMIT TRANSACTION;` commits the transaction if the `INSERT` operation is successful.
   - `ROLLBACK TRANSACTION;` rolls back the transaction in case of an error, preserving data integrity.

3. **Error Handling**:
   - `TRY...CATCH` blocks handle any errors that occur during the execution of the `INSERT` statement.
   - `ERROR_MESSAGE()`, `ERROR_SEVERITY()`, and `ERROR_STATE()` are used to capture and raise detailed error information if an exception occurs.

By using this structure, the stored procedure ensures robust error handling and maintains data consistency through transactions.

---

2. <h3 style="color: tomato; font-weight: 600">Insert Food Type</h3>
  
```sql
   CREATE PROCEDURE InsertFoodType
       @Name NVARCHAR(100),
       @Description NVARCHAR(255),
       @Price DECIMAL(10, 2)
   AS
   BEGIN
       INSERT INTO FoodTypes (Name, Description, Price)
       VALUES (@Name, @Description, @Price);
   END;
   ```

3. **Insert Co-Worker**
 
```sql
   CREATE PROCEDURE InsertCoWorker
       @Name NVARCHAR(100),
       @Role NVARCHAR(50),
       @PhoneNumber NVARCHAR(15),
       @Address NVARCHAR(255)
   AS
   BEGIN
       INSERT INTO CoWorkers (Name, Role, PhoneNumber, Address)
       VALUES (@Name, @Role, @PhoneNumber, @Address);
   END;
   ```

4. <h3 style="color: tomato; font-weight: 600">Insert Ingredient</h3>

```sql
   CREATE PROCEDURE InsertIngredient
       @Name NVARCHAR(100),
       @Quantity NVARCHAR(50),
       @UnitPrice DECIMAL(10, 2)
   AS
   BEGIN
       INSERT INTO Ingredients (Name, Quantity, UnitPrice)
       VALUES (@Name, @Quantity, @UnitPrice);
   END;
   ```

5. <h3 style="color: tomato; font-weight: 600">Insert Income</h3>

```sql
   CREATE PROCEDURE InsertIncome
       @CustomerID INT,
       @FoodTypeID INT,
       @Quantity INT,
       @TotalAmount DECIMAL(10, 2)
   AS
   BEGIN
       INSERT INTO Incomes (CustomerID, FoodTypeID, Quantity, TotalAmount)
       VALUES (@CustomerID, @FoodTypeID, @Quantity, @TotalAmount);
   END;
   ```

6. <h3 style="color: tomato; font-weight: 600">Insert Expenditure</h3>

```sql
   CREATE PROCEDURE InsertExpenditure
       @IngredientID INT,
       @Quantity INT,
       @TotalCost DECIMAL(10, 2)
   AS
   BEGIN
       INSERT INTO Expenditures (IngredientID, Quantity, TotalCost)
       VALUES (@IngredientID, @Quantity, @TotalCost);
   END;
   ```

7. <h3 style="color: tomato; font-weight: 600">Calculate Sales and Purchases</h3>

```sql
   CREATE PROCEDURE CalculateSalesPurchases
       @StartDate DATETIME,
       @EndDate DATETIME
   AS
   BEGIN
       SELECT SUM(TotalAmount) AS TotalSales
       FROM Incomes
       WHERE IncomeDate BETWEEN @StartDate AND @EndDate;

       SELECT SUM(TotalCost) AS TotalPurchases
       FROM Expenditures
       WHERE ExpenditureDate BETWEEN @StartDate AND @EndDate;
   END;
   ```

These table structures and stored procedures will help you manage the food stall's data efficiently. You can create more stored procedures for updating, deleting, and retrieving data as needed for your application.

To provide the Entity-Relationship Diagram (ERD) and Data Flow Diagram (DFD) for the "Mumbai ka Vyapari" application, we first need to analyze the provided SQL table structures and stored procedures.

### Entity-Relationship Diagram (ERD)

The ERD will represent the entities (tables) and their relationships in the database. Here are the main entities based on your SQL schema:

1. **Customers**: Contains customer details like name, phone, email, etc.
2. **FoodTypes**: Lists different types of food items, including their prices and descriptions.
3. **CoWorkers**: Stores information about co-workers such as name, role, phone number, etc.
4. **Ingredients**: Details various ingredients used, their quantities, unit prices, etc.
5. **Incomes**: Represents sales transactions, linking customers and food types with quantities and total amounts.
6. **Expenditures**: Tracks purchases of ingredients, their quantities, and total costs.

#### ERD Breakdown

- **Primary Keys**: Each table has a unique identifier, such as `CustomerID`, `FoodTypeID`, `CoWorkerID`, `ID` (for Ingredients), `IncomeID`, and `ExpenditureID`.
- **Foreign Keys**: 
  - `CustomerID` in **Incomes** references `CustomerID` in **Customers**.
  - `FoodTypeID` in **Incomes** references `FoodTypeID` in **FoodTypes**.
  - `IngredientID` in **Expenditures** references `ID` in **Ingredients**.

Here is a textual representation of the ERD:

```
[Customers]
| CustomerID (PK)
| cName
| cPhone
| cEmail
| cAddress
| cRegistrationDate
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn

[FoodTypes]
| FoodTypeID (PK)
| Name
| Description
| Price
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn

[CoWorkers]
| CoWorkerID (PK)
| cwName
| cwRole
| cwPhone
| cwAddress
| cwHireDate
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn

[Ingredients]
| ID (PK)
| IID (Unique)
| IngrediantName
| Quantity
| UnitPrice
| PurchaseDate
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn

[Incomes]
| IncomeID (PK)
| CustomerID (FK)
| FoodTypeID (FK)
| Quantity
| TotalAmount
| IncomeDate
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn

[Expenditures]
| ExpenditureID (PK)
| IngredientID (FK)
| Quantity
| TotalCost
| ExpenditureDate
| CreatedBy
| CreatedOn
| UpdatedBy
| UpdatedOn
```

#### Relationships:

- **Customers** to **Incomes**: One-to-Many (A customer can have multiple incomes).
- **FoodTypes** to **Incomes**: One-to-Many (A food type can appear in multiple incomes).
- **Ingredients** to **Expenditures**: One-to-Many (An ingredient can appear in multiple expenditures).

### Data Flow Diagram (DFD)

The DFD will illustrate how data flows within the application based on user interactions and stored procedures.

#### Level 0 (Context Level)

1. **External Entities**: 
   - User (Admin, Manager)
   
2. **Processes**:
   - Manage Customers
   - Manage Food Types
   - Manage Co-Workers
   - Manage Ingredients
   - Manage Incomes
   - Manage Expenditures
   - Calculate Sales and Purchases

3. **Data Stores**:
   - Customers Table
   - FoodTypes Table
   - CoWorkers Table
   - Ingredients Table
   - Incomes Table
   - Expenditures Table

```
+-----------------------------------------+
|                 User                    |
|-----------------------------------------|
|   |                                      |
|   |                                      |
v   v                                      v
[Manage Customers]  [Manage Food Types]  [Manage Co-Workers]
|                   |                     |
v                   v                     v
[Customers Table]   [FoodTypes Table]     [CoWorkers Table]

[Manage Ingredients]  [Manage Incomes]  [Manage Expenditures]
|                     |                 |
v                     v                 v
[Ingredients Table]  [Incomes Table]    [Expenditures Table]

              [Calculate Sales and Purchases]
                       |
                       v
            [Sales and Purchases Report]
```

#### Level 1 (Detailed)

1. **Manage Customers**:
   - Insert, Update, Delete, View customers.
   - Data flows to/from **Customers Table**.

2. **Manage Food Types**:
   - Insert, Update, Delete, View food types.
   - Data flows to/from **FoodTypes Table**.

3. **Manage Co-Workers**:
   - Insert, Update, Delete, View co-workers.
   - Data flows to/from **CoWorkers Table**.

4. **Manage Ingredients**:
   - Insert, Update, Delete, View ingredients.
   - Data flows to/from **Ingredients Table**.

5. **Manage Incomes**:
   - Insert, Update, Delete, View incomes.
   - Data flows to/from **Incomes Table**.

6. **Manage Expenditures**:
   - Insert, Update, Delete, View expenditures.
   - Data flows to/from **Expenditures Table**.

7. **Calculate Sales and Purchases**:
   - Generate sales and purchase reports based on date ranges.
   - Data flows to/from **Incomes Table** and **Expenditures Table**.

### Summary

The ERD and DFD provide a comprehensive overview of the data management structure and the flow of operations in the "Mumbai ka Vyapari" application. This setup supports efficient data handling and reporting for managing a food stall’s operations, including customer relations, inventory management, sales tracking, and expense monitoring.