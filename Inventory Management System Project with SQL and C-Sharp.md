Here is a full detailed data structure and database structure for an Inventory Management System. This system will manage products, suppliers, inventory levels, and orders in a warehouse or store. It will cover all core features: product management, supplier management, inventory tracking, and order management.

### Database Structure

#### 1. **Tables and Their Schema**

**1.1.  #Categories Table**
Stores information about product categories.

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `CategoryID`    | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each category.         |
| `CategoryName`  | `VARCHAR(100)`  | `NOT NULL`, `UNIQUE`             | Name of the category.                        |
| `Description`   | `TEXT`          | `NULL`                           | Description of the category.                 |

**1.2. `Suppliers` Table**  
Stores information about suppliers.

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `SupplierID`    | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each supplier.         |
| `SupplierName`  | `VARCHAR(100)`  | `NOT NULL`                       | Name of the supplier.                        |
| `ContactName`   | `VARCHAR(100)`  | `NULL`                           | Contact person at the supplier.              |
| `Phone`         | `VARCHAR(20)`   | `NULL`                           | Supplier's phone number.                     |
| `Email`         | `VARCHAR(100)`  | `NULL`                           | Supplier's email address.                    |
| `Address`       | `VARCHAR(255)`  | `NULL`                           | Supplier's address.                          |
| `City`          | `VARCHAR(50)`   | `NULL`                           | City where the supplier is located.          |
| `PostalCode`    | `VARCHAR(20)`   | `NULL`                           | Postal code of the supplier's location.      |
| `Country`       | `VARCHAR(50)`   | `NULL`                           | Country of the supplier.                     |

**1.3. `Products` Table**  
Stores information about products.

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `ProductID`     | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each product.          |
| `ProductName`   | `VARCHAR(150)`  | `NOT NULL`                       | Name of the product.                         |
| `CategoryID`    | `INT`           | `FOREIGN KEY` references `Categories(CategoryID)`, `NOT NULL` | Category to which the product belongs.       |
| `SupplierID`    | `INT`           | `FOREIGN KEY` references `Suppliers(SupplierID)`, `NOT NULL`   | Supplier of the product.                     |
| `UnitPrice`     | `DECIMAL(18, 2)`| `NOT NULL`                       | Price per unit of the product.               |
| `QuantityPerUnit` | `VARCHAR(50)` | `NULL`                           | Quantity per unit description (e.g., "12 pcs"). |
| `ReorderLevel`  | `INT`           | `NULL`                           | Level at which to reorder stock.             |
| `Discontinued`  | `BIT`           | `NOT NULL`, `DEFAULT 0`          | Whether the product is discontinued.         |
| `UnitsInStock`  | `INT`           | `NOT NULL`, `DEFAULT 0`          | Current units in stock.                      |

**1.4. `InventoryTransactions` Table**  
Tracks inventory changes (adjustments, stock receipts, etc.).

| Column Name         | Data Type       | Constraints                      | Description                                  |
|---------------------|-----------------|----------------------------------|----------------------------------------------|
| `TransactionID`     | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each inventory transaction. |
| `ProductID`         | `INT`           | `FOREIGN KEY` references `Products(ProductID)`, `NOT NULL` | Product involved in the transaction.         |
| `TransactionDate`   | `DATETIME`      | `NOT NULL`                       | Date and time of the transaction.            |
| `Quantity`          | `INT`           | `NOT NULL`                       | Quantity involved in the transaction.        |
| `TransactionType`   | `VARCHAR(50)`   | `NOT NULL`                       | Type of transaction (e.g., 'Receipt', 'Adjustment', 'Sale'). |
| `Remarks`           | `TEXT`          | `NULL`                           | Additional details about the transaction.    |

**1.5. `Orders` Table**  
Stores order information (purchase or sales).

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `OrderID`       | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each order.            |
| `OrderDate`     | `DATETIME`      | `NOT NULL`                       | Date of the order.                           |
| `SupplierID`    | `INT`           | `FOREIGN KEY` references `Suppliers(SupplierID)`, `NULL` | Supplier for purchase orders.               |
| `OrderType`     | `VARCHAR(50)`   | `NOT NULL`                       | Type of order ('Purchase' or 'Sales').       |
| `CustomerName`  | `VARCHAR(100)`  | `NULL`                           | Name of the customer (for sales orders).     |
| `TotalAmount`   | `DECIMAL(18, 2)`| `NOT NULL`                       | Total amount of the order.                   |

**1.6. `OrderDetails` Table**  
Stores detailed information about each product in an order.

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `OrderDetailID` | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each order detail.     |
| `OrderID`       | `INT`           | `FOREIGN KEY` references `Orders(OrderID)`, `NOT NULL` | Associated order.                            |
| `ProductID`     | `INT`           | `FOREIGN KEY` references `Products(ProductID)`, `NOT NULL` | Product being ordered.                       |
| `UnitPrice`     | `DECIMAL(18, 2)`| `NOT NULL`                       | Price per unit of the product.               |
| `Quantity`      | `INT`           | `NOT NULL`                       | Quantity of the product ordered.             |
| `Discount`      | `DECIMAL(5, 2)` | `NOT NULL`, `DEFAULT 0`          | Discount on the product, if any.             |

**1.7. `Users` Table**  
Stores user information for application access and role management.

| Column Name     | Data Type       | Constraints                      | Description                                  |
|-----------------|-----------------|----------------------------------|----------------------------------------------|
| `UserID`        | `INT`           | `PRIMARY KEY`, `IDENTITY(1,1)`   | Unique identifier for each user.             |
| `Username`      | `VARCHAR(50)`   | `NOT NULL`, `UNIQUE`             | Username for login.                          |
| `PasswordHash`  | `VARCHAR(255)`  | `NOT NULL`                       | Hashed password for security.                |
| `Role`          | `VARCHAR(20)`   | `NOT NULL`                       | Role of the user (e.g., 'Admin', 'User').    |

### 2. **Additional Concepts**

#### 2.1. **Stored Procedures**
- **`sp_AddProduct`**: To add a new product to the `Products` table.
- **`sp_UpdateProduct`**: To update product details.
- **`sp_DeleteProduct`**: To delete a product.
- **`sp_GetProductByID`**: To retrieve a product's details by `ProductID`.
- **`sp_CreateOrder`**: To create a new order with associated details in `Orders` and `OrderDetails`.

#### 2.2. **Indexes**
- **Clustered Index**: On primary keys (e.g., `ProductID`, `SupplierID`, `OrderID`).
- **Non-Clustered Index**: On frequently queried columns like `ProductName` in `Products` and `CategoryName` in `Categories`.

#### 2.3. **Triggers**
- **`trg_AfterOrderInsert`**: Automatically adjust inventory levels in `Products` table after an order is placed.
- **`trg_AfterInventoryUpdate`**: Log inventory changes into `InventoryTransactions` after any stock adjustment.

#### 2.4. **Constraints**
- **Foreign Key Constraints**: To ensure referential integrity between tables (e.g., `CategoryID` in `Products` references `Categories`).
- **Check Constraints**: To ensure valid data entry (e.g., `OrderType` must be 'Purchase' or 'Sales').

#### 2.5. **Views**
- **`vw_InventoryStatus`**: A view to show current stock levels for

---

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

1. Create following table:
`Categories` Table: Stores information about product categories.

ID: INT PRIMARY KEY, IDENTITY(1,1)
CategoryID: VARCHAR(5) UNIQUE NOT NULL
CategoryName: VARCHAR(100) NOT NULL
CategoryType: VARCHAR(50) NOT NULL
Description: VARCHAR(500) NULL
isActive: Boolean
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) CategoryID (Flag = C) with Like statement
		ii) CategoryName (Flag = N) with Like statement
		iii) Description (Flag = D) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

--------------------------------------------------------------------------------------------------

Below are the MS SQL Server queries to create the `Categories` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` for error handling.

### 1. Create Table: `Categories`

```sql
CREATE TABLE Categories (
    ID INT PRIMARY KEY IDENTITY(1,1),
    CategoryID VARCHAR(5) UNIQUE NOT NULL,
    CategoryName VARCHAR(100) NOT NULL,
    CategoryType VARCHAR(50) NOT NULL,
    Description VARCHAR(500) NULL,
    isActive BIT NOT NULL,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllCategories`

```sql
CREATE PROCEDURE usp_SelectAllCategories
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Categories;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdCategories`

```sql
CREATE PROCEDURE usp_SelectByIdCategories
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Categories WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDCategories`

```sql
CREATE PROCEDURE usp_GetIDCategories
    @Flag CHAR(1),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'C'
        BEGIN
            SELECT ID FROM Categories WHERE CategoryID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'N'
        BEGIN
            SELECT ID FROM Categories WHERE CategoryName LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'D'
        BEGIN
            SELECT ID FROM Categories WHERE Description LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsCategories`

```sql
CREATE PROCEDURE usp_InsCategories
    @CategoryID VARCHAR(5),
    @CategoryName VARCHAR(100),
    @CategoryType VARCHAR(50),
    @Description VARCHAR(500),
    @isActive BIT,
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;
        
        INSERT INTO Categories (CategoryID, CategoryName, CategoryType, Description, isActive, CreatedBy)
        VALUES (@CategoryID, @CategoryName, @CategoryType, @Description, @isActive, @CreatedBy);
        
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdCategories`

```sql
CREATE PROCEDURE usp_UpdCategories
    @ID INT,
    @CategoryID VARCHAR(5),
    @CategoryName VARCHAR(100),
    @CategoryType VARCHAR(50),
    @Description VARCHAR(500),
    @isActive BIT,
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;
        
        UPDATE Categories
        SET CategoryID = @CategoryID,
            CategoryName = @CategoryName,
            CategoryType = @CategoryType,
            Description = @Description,
            isActive = @isActive,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;
        
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelCategories`

```sql
CREATE PROCEDURE usp_DelCategories
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Categories WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts create the necessary table and stored procedures with transactional handling and error-catching mechanisms, which ensure robust and reliable database operations.

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`Suppliers` Table
Stores information about suppliers.

ID: INT PRIMARY KEY, IDENTITY(1,1)
SupplierID: VARCHAR(5) UNIQUE NOT NULL
SupplierName: VARCHAR(100) NOT NULL
ContactName: VARCHAR(100) NULL
Phone: VARCHAR(20) NULL
Email: VARCHAR(100) NULL
Address: VARCHAR(255) NULL
City: VARCHAR(50) NULL
PostalCode: VARCHAR(20) NULL
Country: VARCHAR(50) NULL
isActive: Boolean
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) SupplierID (Flag = SI) with Like statement
		ii) SupplierName (Flag = SN) with Like statement
		iii) ContactName (Flag = CN) with Like statement
		iv) Phone (Flag = SP) with Like statement
		v) Email (Flag = SE) with Like statement
		vi) Address (Flag = SA) with Like statement
		vii) Description (Flag = D) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

-------------------------------------------------------------------------------------------

Below are the MS SQL Server queries to create the `Suppliers` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` for error handling.

### 1. Create Table: `Suppliers`

```sql
CREATE TABLE Suppliers (
    ID INT PRIMARY KEY IDENTITY(1,1),
    SupplierID VARCHAR(5) UNIQUE NOT NULL,
    SupplierName VARCHAR(100) NOT NULL,
    ContactName VARCHAR(100) NULL,
    Phone VARCHAR(20) NULL,
    Email VARCHAR(100) NULL,
    Address VARCHAR(255) NULL,
    City VARCHAR(50) NULL,
    PostalCode VARCHAR(20) NULL,
    Country VARCHAR(50) NULL,
    isActive BIT NOT NULL,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllSuppliers`

```sql
CREATE PROCEDURE usp_SelectAllSuppliers
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Suppliers;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdSuppliers`

```sql
CREATE PROCEDURE usp_SelectByIdSuppliers
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Suppliers WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDSuppliers`

```sql
CREATE PROCEDURE usp_GetIDSuppliers
    @Flag CHAR(2),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'SI'
        BEGIN
            SELECT ID FROM Suppliers WHERE SupplierID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SN'
        BEGIN
            SELECT ID FROM Suppliers WHERE SupplierName LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'CN'
        BEGIN
            SELECT ID FROM Suppliers WHERE ContactName LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SP'
        BEGIN
            SELECT ID FROM Suppliers WHERE Phone LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SE'
        BEGIN
            SELECT ID FROM Suppliers WHERE Email LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SA'
        BEGIN
            SELECT ID FROM Suppliers WHERE Address LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsSuppliers`

```sql
CREATE PROCEDURE usp_InsSuppliers
    @SupplierID VARCHAR(5),
    @SupplierName VARCHAR(100),
    @ContactName VARCHAR(100),
    @Phone VARCHAR(20),
    @Email VARCHAR(100),
    @Address VARCHAR(255),
    @City VARCHAR(50),
    @PostalCode VARCHAR(20),
    @Country VARCHAR(50),
    @isActive BIT,
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;
        
        INSERT INTO Suppliers (SupplierID, SupplierName, ContactName, Phone, Email, Address, City, PostalCode, Country, isActive, CreatedBy)
        VALUES (@SupplierID, @SupplierName, @ContactName, @Phone, @Email, @Address, @City, @PostalCode, @Country, @isActive, @CreatedBy);
        
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdSuppliers`

```sql
CREATE PROCEDURE usp_UpdSuppliers
    @ID INT,
    @SupplierID VARCHAR(5),
    @SupplierName VARCHAR(100),
    @ContactName VARCHAR(100),
    @Phone VARCHAR(20),
    @Email VARCHAR(100),
    @Address VARCHAR(255),
    @City VARCHAR(50),
    @PostalCode VARCHAR(20),
    @Country VARCHAR(50),
    @isActive BIT,
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;
        
        UPDATE Suppliers
        SET SupplierID = @SupplierID,
            SupplierName = @SupplierName,
            ContactName = @ContactName,
            Phone = @Phone,
            Email = @Email,
            Address = @Address,
            City = @City,
            PostalCode = @PostalCode,
            Country = @Country,
            isActive = @isActive,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;
        
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelSuppliers`

```sql
CREATE PROCEDURE usp_DelSuppliers
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Suppliers WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts create the necessary table and stored procedures with transactional handling and error-catching mechanisms to ensure robust and reliable database operations.

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`Products` Table
Stores information about products.

ID: INT PRIMARY KEY, IDENTITY(1,1)
ProductID: VARCHAR(5) UNIQUE NOT NULL
ProductName: VARCHAR(150) NOT NULL
ProductDesc: VARCHAR(500) NULL
CategoryID: INT FOREIGN KEY references Categories(CategoryID), NOT NULL
SupplierID: INT FOREIGN KEY references Suppliers(SupplierID), NOT NULL
UnitPrice: DECIMAL(18, 2) NOT NULL
QuantityPerUnit: VARCHAR(50) NULL
ReorderLevel: INT NULL
Discontinued: BIT NOT NULL, DEFAULT 0 (Whether the product is discontinued)
UnitsInStock: INT NOT NULL, DEFAULT 0 (Current units in stock)
isActive: Boolean
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) ProductID (Flag = PI) with Like statement
		i) CategoryID (Flag = CI) with Like statement
		i) SupplierID (Flag = SI) with Like statement
		ii) ProductName (Flag = PN) with Like statement
		vii) ProductDesc (Flag = PD) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

-------------------------------------------------------------------------------------------

Below are the MS SQL Server queries to create the `Products` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` blocks for error handling.

### 1. Create Table: `Products`

```sql
CREATE TABLE Products (
    ID INT PRIMARY KEY IDENTITY(1,1),
    ProductID VARCHAR(5) UNIQUE NOT NULL,
    ProductName VARCHAR(150) NOT NULL,
    ProductDesc VARCHAR(500) NULL,
    CategoryID INT NOT NULL FOREIGN KEY REFERENCES Categories(ID),
    SupplierID INT NOT NULL FOREIGN KEY REFERENCES Suppliers(ID),
    UnitPrice DECIMAL(18, 2) NOT NULL,
    QuantityPerUnit VARCHAR(50) NULL,
    ReorderLevel INT NULL,
    Discontinued BIT NOT NULL DEFAULT 0,
    UnitsInStock INT NOT NULL DEFAULT 0,
    isActive BIT NOT NULL,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllProducts`

```sql
CREATE PROCEDURE usp_SelectAllProducts
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Products;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdProducts`

```sql
CREATE PROCEDURE usp_SelectByIdProducts
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Products WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDProducts`

```sql
CREATE PROCEDURE usp_GetIDProducts
    @Flag CHAR(2),
    @SearchValue VARCHAR(150)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'PI'
        BEGIN
            SELECT ID FROM Products WHERE ProductID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'CI'
        BEGIN
            SELECT ID FROM Products WHERE CAST(CategoryID AS VARCHAR) LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SI'
        BEGIN
            SELECT ID FROM Products WHERE CAST(SupplierID AS VARCHAR) LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'PN'
        BEGIN
            SELECT ID FROM Products WHERE ProductName LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'PD'
        BEGIN
            SELECT ID FROM Products WHERE ProductDesc LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsProducts`

```sql
CREATE PROCEDURE usp_InsProducts
    @ProductID VARCHAR(5),
    @ProductName VARCHAR(150),
    @ProductDesc VARCHAR(500),
    @CategoryID INT,
    @SupplierID INT,
    @UnitPrice DECIMAL(18, 2),
    @QuantityPerUnit VARCHAR(50),
    @ReorderLevel INT,
    @Discontinued BIT,
    @UnitsInStock INT,
    @isActive BIT,
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Products (
            ProductID, ProductName, ProductDesc, CategoryID, SupplierID, 
            UnitPrice, QuantityPerUnit, ReorderLevel, Discontinued, UnitsInStock, 
            isActive, CreatedBy
        ) VALUES (
            @ProductID, @ProductName, @ProductDesc, @CategoryID, @SupplierID, 
            @UnitPrice, @QuantityPerUnit, @ReorderLevel, @Discontinued, @UnitsInStock, 
            @isActive, @CreatedBy
        );

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdProducts`

```sql
CREATE PROCEDURE usp_UpdProducts
    @ID INT,
    @ProductID VARCHAR(5),
    @ProductName VARCHAR(150),
    @ProductDesc VARCHAR(500),
    @CategoryID INT,
    @SupplierID INT,
    @UnitPrice DECIMAL(18, 2),
    @QuantityPerUnit VARCHAR(50),
    @ReorderLevel INT,
    @Discontinued BIT,
    @UnitsInStock INT,
    @isActive BIT,
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Products
        SET 
            ProductID = @ProductID,
            ProductName = @ProductName,
            ProductDesc = @ProductDesc,
            CategoryID = @CategoryID,
            SupplierID = @SupplierID,
            UnitPrice = @UnitPrice,
            QuantityPerUnit = @QuantityPerUnit,
            ReorderLevel = @ReorderLevel,
            Discontinued = @Discontinued,
            UnitsInStock = @UnitsInStock,
            isActive = @isActive,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelProducts`

```sql
CREATE PROCEDURE usp_DelProducts
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Products WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts create the `Products` table and associated stored procedures with robust error handling and transactional support to ensure reliable database operations.

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`InventoryTransactions` Table
Tracks inventory changes (adjustments, stock receipts, etc.)

ID: INT PRIMARY KEY, IDENTITY(1,1)
TransactionID: VARCHAR(5) UNIQUE NOT NULL (Unique identifier for each inventory transaction)
ProductID: INT FOREIGN KEY references Products(ProductID), NOT NULL (Product involved in the transaction)
TransactionDate: DATETIME NOT NULL (Date and time of the transaction
Quantity: INT NOT NULL
TransactionType: VARCHAR(50) NOT NULL (Type of transaction (e.g., 'Receipt', 'Adjustment', 'Sale'))
Remarks: VARCHAR(500) NULL
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) TransactionID (Flag = TI) with Like statement
		ii) TransactionDate (Flag = TD) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

-------------------------------------------------------------------------------------------

Below are the MS SQL Server queries to create the `InventoryTransactions` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` blocks for error handling.

### 1. Create Table: `InventoryTransactions`

```sql
CREATE TABLE InventoryTransactions (
    ID INT PRIMARY KEY IDENTITY(1,1),
    TransactionID VARCHAR(5) UNIQUE NOT NULL,
    ProductID INT NOT NULL FOREIGN KEY REFERENCES Products(ID),
    TransactionDate DATETIME NOT NULL,
    Quantity INT NOT NULL,
    TransactionType VARCHAR(50) NOT NULL,
    Remarks VARCHAR(500) NULL,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllInventoryTransactions`

```sql
CREATE PROCEDURE usp_SelectAllInventoryTransactions
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM InventoryTransactions;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdInventoryTransactions`

```sql
CREATE PROCEDURE usp_SelectByIdInventoryTransactions
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM InventoryTransactions WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDInventoryTransactions`

```sql
CREATE PROCEDURE usp_GetIDInventoryTransactions
    @Flag CHAR(2),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'TI'
        BEGIN
            SELECT ID FROM InventoryTransactions WHERE TransactionID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'TD'
        BEGIN
            SELECT ID FROM InventoryTransactions WHERE CONVERT(VARCHAR, TransactionDate, 120) LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsInventoryTransactions`

```sql
CREATE PROCEDURE usp_InsInventoryTransactions
    @TransactionID VARCHAR(5),
    @ProductID INT,
    @TransactionDate DATETIME,
    @Quantity INT,
    @TransactionType VARCHAR(50),
    @Remarks VARCHAR(500),
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO InventoryTransactions (
            TransactionID, ProductID, TransactionDate, Quantity, 
            TransactionType, Remarks, CreatedBy
        ) VALUES (
            @TransactionID, @ProductID, @TransactionDate, @Quantity, 
            @TransactionType, @Remarks, @CreatedBy
        );

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdInventoryTransactions`

```sql
CREATE PROCEDURE usp_UpdInventoryTransactions
    @ID INT,
    @TransactionID VARCHAR(5),
    @ProductID INT,
    @TransactionDate DATETIME,
    @Quantity INT,
    @TransactionType VARCHAR(50),
    @Remarks VARCHAR(500),
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE InventoryTransactions
        SET 
            TransactionID = @TransactionID,
            ProductID = @ProductID,
            TransactionDate = @TransactionDate,
            Quantity = @Quantity,
            TransactionType = @TransactionType,
            Remarks = @Remarks,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelInventoryTransactions`

```sql
CREATE PROCEDURE usp_DelInventoryTransactions
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM InventoryTransactions WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts define the `InventoryTransactions` table and the stored procedures for managing inventory transactions, incorporating robust error handling and transactional support.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`Orders` Table
Stores order information (purchase or sales).

ID: INT PRIMARY KEY, IDENTITY(1,1)
OrderID: VARCHAR(5) UNIQUE NOT NULL (Unique identifier for each orders)
OrderDate: DATETIME NOT NULL
SupplierID: INT FOREIGN KEY references Suppliers(SupplierID), NULL (Supplier for purchase orders)
OrderType: VARCHAR(50) NOT NULL (Type of order ('Purchase' or 'Sales'))
CustomerName: VARCHAR(100) NULL (Name of the customer (for sales orders))
TotalAmount: DECIMAL(18, 2) NOT NULL
isActive: Boolean
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) OrderID (Flag = OI) with Like statement
		ii) OrderDate (Flag = OD) with Like statement
		iii) SupplierID (Flag = SI) with Like statement
		iv) CustomerName (Flag = CN) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

-------------------------------------------------------------------------------------------

Below are the MS SQL Server queries for creating the `Orders` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` blocks for error handling.

### 1. Create Table: `Orders`

```sql
CREATE TABLE Orders (
    ID INT PRIMARY KEY IDENTITY(1,1),
    OrderID VARCHAR(5) UNIQUE NOT NULL,
    OrderDate DATETIME NOT NULL,
    SupplierID INT NULL FOREIGN KEY REFERENCES Suppliers(ID),
    OrderType VARCHAR(50) NOT NULL,
    CustomerName VARCHAR(100) NULL,
    TotalAmount DECIMAL(18, 2) NOT NULL,
    isActive BIT NOT NULL DEFAULT 1,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllOrders`

```sql
CREATE PROCEDURE usp_SelectAllOrders
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Orders;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdOrders`

```sql
CREATE PROCEDURE usp_SelectByIdOrders
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Orders WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDOrders`

```sql
CREATE PROCEDURE usp_GetIDOrders
    @Flag CHAR(2),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'OI'
        BEGIN
            SELECT ID FROM Orders WHERE OrderID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'OD'
        BEGIN
            SELECT ID FROM Orders WHERE CONVERT(VARCHAR, OrderDate, 120) LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'SI'
        BEGIN
            SELECT ID FROM Orders WHERE SupplierID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'CN'
        BEGIN
            SELECT ID FROM Orders WHERE CustomerName LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsOrders`

```sql
CREATE PROCEDURE usp_InsOrders
    @OrderID VARCHAR(5),
    @OrderDate DATETIME,
    @SupplierID INT NULL,
    @OrderType VARCHAR(50),
    @CustomerName VARCHAR(100) NULL,
    @TotalAmount DECIMAL(18, 2),
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Orders (
            OrderID, OrderDate, SupplierID, OrderType, 
            CustomerName, TotalAmount, CreatedBy
        ) VALUES (
            @OrderID, @OrderDate, @SupplierID, @OrderType, 
            @CustomerName, @TotalAmount, @CreatedBy
        );

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdOrders`

```sql
CREATE PROCEDURE usp_UpdOrders
    @ID INT,
    @OrderID VARCHAR(5),
    @OrderDate DATETIME,
    @SupplierID INT NULL,
    @OrderType VARCHAR(50),
    @CustomerName VARCHAR(100) NULL,
    @TotalAmount DECIMAL(18, 2),
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Orders
        SET 
            OrderID = @OrderID,
            OrderDate = @OrderDate,
            SupplierID = @SupplierID,
            OrderType = @OrderType,
            CustomerName = @CustomerName,
            TotalAmount = @TotalAmount,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelOrders`

```sql
CREATE PROCEDURE usp_DelOrders
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Orders WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts define the `Orders` table and the associated stored procedures for managing order records, incorporating robust error handling and transactional support.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`OrderDetails` Table
Stores detailed information about each product in an order.

ID: INT PRIMARY KEY, IDENTITY(1,1)
OrderDetailID: VARCHAR(5) UNIQUE NOT NULL (Unique identifier for each orders)
OrderID: INT FOREIGN KEY references Orders(OrderID) NOT NULL (Associated order)
ProductID: INT FOREIGN KEY references Products(ProductID) NOT NULL (Product being ordered)
UnitPrice: DECIMAL(18, 2) NOT NULL (Price per unit of the product)
Quantity: INT NOT NULL (Quantity of the product ordered)
Discount: DECIMAL(5, 2) NOT NULL DEFAULT 0 (Discount on the product, if any)
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) OrderDetailID (Flag = DI) with Like statement
		ii) OrderID (Flag = OI) with Like statement
		iii) ProductID (Flag = PI) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

----------------------------------------------------------------------------------------------

Below are the SQL Server queries for creating the `OrderDetails` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` blocks for error handling.

### 1. Create Table: `OrderDetails`

```sql
CREATE TABLE OrderDetails (
    ID INT PRIMARY KEY IDENTITY(1,1),
    OrderDetailID VARCHAR(5) UNIQUE NOT NULL,
    OrderID INT NOT NULL FOREIGN KEY REFERENCES Orders(ID),
    ProductID INT NOT NULL FOREIGN KEY REFERENCES Products(ID),
    UnitPrice DECIMAL(18, 2) NOT NULL,
    Quantity INT NOT NULL,
    Discount DECIMAL(5, 2) NOT NULL DEFAULT 0,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllOrderDetails`

```sql
CREATE PROCEDURE usp_SelectAllOrderDetails
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM OrderDetails;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdOrderDetails`

```sql
CREATE PROCEDURE usp_SelectByIdOrderDetails
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM OrderDetails WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDOrderDetails`

```sql
CREATE PROCEDURE usp_GetIDOrderDetails
    @Flag CHAR(2),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'DI'
        BEGIN
            SELECT ID FROM OrderDetails WHERE OrderDetailID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'OI'
        BEGIN
            SELECT ID FROM OrderDetails WHERE OrderID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'PI'
        BEGIN
            SELECT ID FROM OrderDetails WHERE ProductID LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsOrderDetails`

```sql
CREATE PROCEDURE usp_InsOrderDetails
    @OrderDetailID VARCHAR(5),
    @OrderID INT,
    @ProductID INT,
    @UnitPrice DECIMAL(18, 2),
    @Quantity INT,
    @Discount DECIMAL(5, 2) = 0,
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO OrderDetails (
            OrderDetailID, OrderID, ProductID, UnitPrice,
            Quantity, Discount, CreatedBy
        ) VALUES (
            @OrderDetailID, @OrderID, @ProductID, @UnitPrice,
            @Quantity, @Discount, @CreatedBy
        );

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdOrderDetails`

```sql
CREATE PROCEDURE usp_UpdOrderDetails
    @ID INT,
    @OrderDetailID VARCHAR(5),
    @OrderID INT,
    @ProductID INT,
    @UnitPrice DECIMAL(18, 2),
    @Quantity INT,
    @Discount DECIMAL(5, 2) = 0,
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE OrderDetails
        SET 
            OrderDetailID = @OrderDetailID,
            OrderID = @OrderID,
            ProductID = @ProductID,
            UnitPrice = @UnitPrice,
            Quantity = @Quantity,
            Discount = @Discount,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelOrderDetails`

```sql
CREATE PROCEDURE usp_DelOrderDetails
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM OrderDetails WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These SQL scripts create the `OrderDetails` table and associated stored procedures, with proper error handling and transaction management for robust database operations.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Generate MS SQL Server Queries with transactional statements and Try-Catch Staement for the following Table Structure

`Users` Table
Stores user information for application access and role management.

ID: INT PRIMARY KEY, IDENTITY(1,1)
UserID: VARCHAR(5) UNIQUE NOT NULL (Unique identifier for each orders)
Username	VARCHAR(50)	NOT NULL, UNIQUE	Username for login.
PasswordHash	VARCHAR(255)	NOT NULL	Hashed password for security.
usrPicture: Image (Users Profile Picture)
Role	VARCHAR(20)	NOT NULL	Role of the user (e.g., 'Admin', 'User').
isActive: Boolean
CreatedBy: VARCHAR(50)
CreationDate: DATETIME DEFAULT GETDATE()
UpdatedBy: VARCHAR(50)
UpdationDate: DATETIME

Create stored procedures for above table:
1. Select Queries:
	a. Select All Procedure: Stored procedure name prefix should be `usp_SelectAll`
	b. Select By ID Procedure: Stored procedure name prefix should be `usp_SelectById`
	c. Get ID By if user provides the following columns value with flag:
		i) UserID (Flag = UI) with Like statement
		ii) Username (Flag = UN) with Like statement
			Procedure: Stored procedure name prefix should be `usp_GetID`
	d. Insert New Record Procedure: Stored procedure name prefix should be `usp_Ins`
	e. Update Existing Record Procedure: Stored procedure name prefix should be `usp_Upd`
	f. Delete Existing Record Procedure: Stored procedure name prefix should be `usp_Del`

----------------------------------------------------------------------------------------------

Here are the SQL Server queries for creating the `Users` table and the corresponding stored procedures with transactional statements and `TRY-CATCH` blocks for error handling.

### 1. Create Table: `Users`

```sql
CREATE TABLE Users (
    ID INT PRIMARY KEY IDENTITY(1,1),
    UserID VARCHAR(5) UNIQUE NOT NULL,
    Username VARCHAR(50) NOT NULL UNIQUE,
    PasswordHash VARCHAR(255) NOT NULL,
    usrPicture VARBINARY(MAX) NULL, -- Changed from IMAGE to VARBINARY(MAX)
    Role VARCHAR(20) NOT NULL,
    isActive BIT NOT NULL,
    CreatedBy VARCHAR(50),
    CreationDate DATETIME DEFAULT GETDATE(),
    UpdatedBy VARCHAR(50),
    UpdationDate DATETIME
);
```

### 2. Stored Procedures

#### a. Select All Procedure: `usp_SelectAllUsers`

```sql
CREATE PROCEDURE usp_SelectAllUsers
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Users;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### b. Select By ID Procedure: `usp_SelectByIdUser`

```sql
CREATE PROCEDURE usp_SelectByIdUser
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        SELECT * FROM Users WHERE ID = @ID;
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### c. Get ID By Provided Column Value: `usp_GetIDUsers`

```sql
CREATE PROCEDURE usp_GetIDUsers
    @Flag CHAR(2),
    @SearchValue VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        IF @Flag = 'UI'
        BEGIN
            SELECT ID FROM Users WHERE UserID LIKE '%' + @SearchValue + '%';
        END
        ELSE IF @Flag = 'UN'
        BEGIN
            SELECT ID FROM Users WHERE Username LIKE '%' + @SearchValue + '%';
        END
    END TRY
    BEGIN CATCH
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### d. Insert New Record Procedure: `usp_InsUser`

```sql
CREATE PROCEDURE usp_InsUser
    @UserID VARCHAR(5),
    @Username VARCHAR(50),
    @PasswordHash VARCHAR(255),
    @usrPicture VARBINARY(MAX) = NULL, -- Changed from IMAGE to VARBINARY(MAX)
    @Role VARCHAR(20),
    @isActive BIT,
    @CreatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO Users (
            UserID, Username, PasswordHash, usrPicture, Role,
            isActive, CreatedBy
        ) VALUES (
            @UserID, @Username, @PasswordHash, @usrPicture, @Role,
            @isActive, @CreatedBy
        );

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### e. Update Existing Record Procedure: `usp_UpdUser`

```sql
CREATE PROCEDURE usp_UpdUser
    @ID INT,
    @UserID VARCHAR(5),
    @Username VARCHAR(50),
    @PasswordHash VARCHAR(255),
    @usrPicture VARBINARY(MAX) = NULL, -- Changed from IMAGE to VARBINARY(MAX)
    @Role VARCHAR(20),
    @isActive BIT,
    @UpdatedBy VARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE Users
        SET 
            UserID = @UserID,
            Username = @Username,
            PasswordHash = @PasswordHash,
            usrPicture = @usrPicture,
            Role = @Role,
            isActive = @isActive,
            UpdatedBy = @UpdatedBy,
            UpdationDate = GETDATE()
        WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

#### f. Delete Existing Record Procedure: `usp_DelUser`

```sql
CREATE PROCEDURE usp_DelUser
    @ID INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        DELETE FROM Users WHERE ID = @ID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        -- Error handling
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

These stored procedures include transactional control and error handling to ensure reliable database operations for the `Users` table.

---

#Prompts
Table:
Categories
Suppliers
Products
InventoryTransactions
Orders
OrderDetails
Users

Stored Procedures

a. Select All Procedure: `usp_SelectAllCategories`
b. Select By ID Procedure: `usp_SelectByIdCategories`
c. Get ID By Provided Column Value: `usp_GetIDCategories`
d. Insert New Record Procedure: `usp_InsCategories`
e. Update Existing Record Procedure: `usp_UpdCategories`
f. Delete Existing Record Procedure: `usp_DelCategories`

a. Select All Procedure: `usp_SelectAllSuppliers`
b. Select By ID Procedure: `usp_SelectByIdSuppliers`
c. Get ID By Provided Column Value: `usp_GetIDSuppliers`
d. Insert New Record Procedure: `usp_InsSuppliers`
e. Update Existing Record Procedure: `usp_UpdSuppliers`
f. Delete Existing Record Procedure: `usp_DelSuppliers`

a. Select All Procedure: `usp_SelectAllProducts`
b. Select By ID Procedure: `usp_SelectByIdProducts`
c. Get ID By Provided Column Value: `usp_GetIDProducts`
d. Insert New Record Procedure: `usp_InsProducts`
e. Update Existing Record Procedure: `usp_UpdProducts`
f. Delete Existing Record Procedure: `usp_DelProducts`

a. Select All Procedure: `usp_SelectAllInventoryTransactions`
b. Select By ID Procedure: `usp_SelectByIdInventoryTransactions`
c. Get ID By Provided Column Value: `usp_GetIDInventoryTransactions`
d. Insert New Record Procedure: `usp_InsInventoryTransactions`
e. Update Existing Record Procedure: `usp_UpdInventoryTransactions`
f. Delete Existing Record Procedure: `usp_DelInventoryTransactions`

a. Select All Procedure: `usp_SelectAllOrders`
b. Select By ID Procedure: `usp_SelectByIdOrders`
c. Get ID By Provided Column Value: `usp_GetIDOrders`
d. Insert New Record Procedure: `usp_InsOrders`
e. Update Existing Record Procedure: `usp_UpdOrders`
f. Delete Existing Record Procedure: `usp_DelOrders`

a. Select All Procedure: `usp_SelectAllOrderDetails`
b. Select By ID Procedure: `usp_SelectByIdOrderDetails`
c. Get ID By Provided Column Value: `usp_GetIDOrderDetails`
d. Insert New Record Procedure: `usp_InsOrderDetails`
e. Update Existing Record Procedure: `usp_UpdOrderDetails`
f. Delete Existing Record Procedure: `usp_DelOrderDetails`

a. Select All Procedure: `usp_SelectAllUsers`
b. Select By ID Procedure: `usp_SelectByIdUser`
c. Get ID By Provided Column Value: `usp_GetIDUsers`
d. Insert New Record Procedure: `usp_InsUser`
e. Update Existing Record Procedure: `usp_UpdUser`
f. Delete Existing Record Procedure: `usp_DelUser`


Generated Model via EF 6.0x

namespace Database_Entity
{
    using System;
    using System.Collections.Generic;
    
    public partial class User
    {
        public int ID { get; set; }
        public string UserID { get; set; }
        public string Username { get; set; }
        public string PasswordHash { get; set; }
        public byte[] usrPicture { get; set; }
        public string Role { get; set; }
        public bool isActive { get; set; }
        public string CreatedBy { get; set; }
        public Nullable<System.DateTime> CreationDate { get; set; }
        public string UpdatedBy { get; set; }
        public Nullable<System.DateTime> UpdationDate { get; set; }
    }
}

Generated DbContext via EF 6.0x

namespace Database_Entity
{
    using System;
    using System.Data.Entity;
    using System.Data.Entity.Infrastructure;
    using System.Data.Entity.Core.Objects;
    using System.Linq;
    
    public partial class DevShrishDBEntities : DbContext
    {
        public DevShrishDBEntities()
            : base("name=DevShrishDBEntities")
        {
        }
    
        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            throw new UnintentionalCodeFirstException();
        }

        public virtual DbSet<User> Users { get; set; }
    
        public virtual int usp_DelUser(Nullable<int> iD)
        {
            var iDParameter = iD.HasValue ?
                new ObjectParameter("ID", iD) :
                new ObjectParameter("ID", typeof(int));
    
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction("usp_DelUser", iDParameter);
        }
    
        public virtual ObjectResult<Nullable<int>> usp_GetIDUsers(string flag, string searchValue)
        {
            var flagParameter = flag != null ?
                new ObjectParameter("Flag", flag) :
                new ObjectParameter("Flag", typeof(string));
    
            var searchValueParameter = searchValue != null ?
                new ObjectParameter("SearchValue", searchValue) :
                new ObjectParameter("SearchValue", typeof(string));
    
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction<Nullable<int>>("usp_GetIDUsers", flagParameter, searchValueParameter);
        }
    
        public virtual int usp_InsUser(string userID, string username, string passwordHash, byte[] usrPicture, string role, Nullable<bool> isActive, string createdBy)
        {
            var userIDParameter = userID != null ?
                new ObjectParameter("UserID", userID) :
                new ObjectParameter("UserID", typeof(string));
    
            var usernameParameter = username != null ?
                new ObjectParameter("Username", username) :
                new ObjectParameter("Username", typeof(string));
    
            var passwordHashParameter = passwordHash != null ?
                new ObjectParameter("PasswordHash", passwordHash) :
                new ObjectParameter("PasswordHash", typeof(string));
    
            var usrPictureParameter = usrPicture != null ?
                new ObjectParameter("usrPicture", usrPicture) :
                new ObjectParameter("usrPicture", typeof(byte[]));
    
            var roleParameter = role != null ?
                new ObjectParameter("Role", role) :
                new ObjectParameter("Role", typeof(string));
    
            var isActiveParameter = isActive.HasValue ?
                new ObjectParameter("isActive", isActive) :
                new ObjectParameter("isActive", typeof(bool));
    
            var createdByParameter = createdBy != null ?
                new ObjectParameter("CreatedBy", createdBy) :
                new ObjectParameter("CreatedBy", typeof(string));
    
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction("usp_InsUser", userIDParameter, usernameParameter, passwordHashParameter, usrPictureParameter, roleParameter, isActiveParameter, createdByParameter);
        }

        public virtual ObjectResult<usp_SelectAllUsers_Result> usp_SelectAllUsers()
        {
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction<usp_SelectAllUsers_Result>("usp_SelectAllUsers");
        }
        public virtual ObjectResult<usp_SelectByIdUser_Result> usp_SelectByIdUser(Nullable<int> iD)
        {
            var iDParameter = iD.HasValue ?
                new ObjectParameter("ID", iD) :
                new ObjectParameter("ID", typeof(int));
    
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction<usp_SelectByIdUser_Result>("usp_SelectByIdUser", iDParameter);
        }
        public virtual int usp_UpdUser(Nullable<int> iD, string userID, string username, string passwordHash, byte[] usrPicture, string role, Nullable<bool> isActive, string updatedBy)
        {
            var iDParameter = iD.HasValue ?
                new ObjectParameter("ID", iD) :
                new ObjectParameter("ID", typeof(int));
    
            var userIDParameter = userID != null ?
                new ObjectParameter("UserID", userID) :
                new ObjectParameter("UserID", typeof(string));
    
            var usernameParameter = username != null ?
                new ObjectParameter("Username", username) :
                new ObjectParameter("Username", typeof(string));
    
            var passwordHashParameter = passwordHash != null ?
                new ObjectParameter("PasswordHash", passwordHash) :
                new ObjectParameter("PasswordHash", typeof(string));
    
            var usrPictureParameter = usrPicture != null ?
                new ObjectParameter("usrPicture", usrPicture) :
                new ObjectParameter("usrPicture", typeof(byte[]));
    
            var roleParameter = role != null ?
                new ObjectParameter("Role", role) :
                new ObjectParameter("Role", typeof(string));
    
            var isActiveParameter = isActive.HasValue ?
                new ObjectParameter("isActive", isActive) :
                new ObjectParameter("isActive", typeof(bool));
    
            var updatedByParameter = updatedBy != null ?
                new ObjectParameter("UpdatedBy", updatedBy) :
                new ObjectParameter("UpdatedBy", typeof(string));
    
            return ((IObjectContextAdapter)this).ObjectContext.ExecuteFunction("usp_UpdUser", iDParameter, userIDParameter, usernameParameter, passwordHashParameter, usrPictureParameter, roleParameter, isActiveParameter, updatedByParameter);
        }
    }
}


Write DataAccess and BuisnessObject Class Code for the following EF Model with Windows Form App UI






Certainly! I'll guide you through creating a Data Access Layer (DAL), Business Logic Layer (BLL), and a Windows Forms User Interface (UI) for managing users based on the Entity Framework (EF) model you've provided. 

### 1. Data Access Layer (DAL)

The DAL is responsible for interacting with the database. Below is a sample `UserDataAccess` class that handles CRUD operations.

```csharp
// DataAccess/UserDataAccess.cs
using System;
using System.Linq;
using System.Collections.Generic;
using Database_Entity;

public class UserDataAccess
{
    private readonly DevShrishDBEntities _context;

    public UserDataAccess()
    {
        _context = new DevShrishDBEntities();
    }

    public User GetUserById(int id)
    {
        var result = _context.usp_SelectByIdUser(id).FirstOrDefault();
        return result != null ? MapToUser(result) : null;
    }

    public List<User> GetAllUsers()
    {
        return _context.usp_SelectAllUsers().Select(MapToUser).ToList();
    }

    public int AddUser(User user)
    {
        return _context.usp_InsUser(user.UserID, user.Username, user.PasswordHash, user.usrPicture, user.Role, user.isActive, user.CreatedBy);
    }

    public int UpdateUser(User user)
    {
        return _context.usp_UpdUser(user.ID, user.UserID, user.Username, user.PasswordHash, user.usrPicture, user.Role, user.isActive, user.UpdatedBy);
    }

    public int DeleteUser(int id)
    {
        return _context.usp_DelUser(id);
    }

    private User MapToUser(usp_SelectAllUsers_Result result)
    {
        return new User
        {
            ID = result.ID,
            UserID = result.UserID,
            Username = result.Username,
            PasswordHash = result.PasswordHash,
            usrPicture = result.usrPicture,
            Role = result.Role,
            isActive = result.isActive,
            CreatedBy = result.CreatedBy,
            CreationDate = result.CreationDate,
            UpdatedBy = result.UpdatedBy,
            UpdationDate = result.UpdationDate
        };
    }
}
```

### 2. Business Logic Layer (BLL)

The BLL contains business logic and interacts with the DAL.

```csharp
// BusinessLogic/UserManager.cs
using System.Collections.Generic;

public class UserManager
{
    private readonly UserDataAccess _dataAccess;

    public UserManager()
    {
        _dataAccess = new UserDataAccess();
    }

    public User GetUser(int id)
    {
        return _dataAccess.GetUserById(id);
    }

    public List<User> GetUsers()
    {
        return _dataAccess.GetAllUsers();
    }

    public bool AddUser(User user)
    {
        var result = _dataAccess.AddUser(user);
        return result > 0;
    }

    public bool UpdateUser(User user)
    {
        var result = _dataAccess.UpdateUser(user);
        return result > 0;
    }

    public bool DeleteUser(int id)
    {
        var result = _dataAccess.DeleteUser(id);
        return result > 0;
    }
}
```

### 3. Windows Forms UI

Here’s an example of a basic UI to view, add, update, and delete users.

#### Form for Viewing and Managing Users

```csharp
// UserForm.cs
using System;
using System.Collections.Generic;
using System.Windows.Forms;

public partial class UserForm : Form
{
    private readonly UserManager _userManager;
    private User _currentUser;

    public UserForm()
    {
        InitializeComponent();
        _userManager = new UserManager();
        LoadUsers();
    }

    private void LoadUsers()
    {
        var users = _userManager.GetUsers();
        dataGridViewUsers.DataSource = users;
    }

    private void buttonAdd_Click(object sender, EventArgs e)
    {
        var user = GetUserFromInput();
        if (_userManager.AddUser(user))
        {
            MessageBox.Show("User added successfully!");
            LoadUsers();
        }
        else
        {
            MessageBox.Show("Error adding user.");
        }
    }

    private void buttonUpdate_Click(object sender, EventArgs e)
    {
        if (_currentUser != null)
        {
            var user = GetUserFromInput();
            user.ID = _currentUser.ID;
            if (_userManager.UpdateUser(user))
            {
                MessageBox.Show("User updated successfully!");
                LoadUsers();
            }
            else
            {
                MessageBox.Show("Error updating user.");
            }
        }
    }

    private void buttonDelete_Click(object sender, EventArgs e)
    {
        if (_currentUser != null)
        {
            if (_userManager.DeleteUser(_currentUser.ID))
            {
                MessageBox.Show("User deleted successfully!");
                LoadUsers();
            }
            else
            {
                MessageBox.Show("Error deleting user.");
            }
        }
    }

    private void dataGridViewUsers_SelectionChanged(object sender, EventArgs e)
    {
        if (dataGridViewUsers.SelectedRows.Count > 0)
        {
            var selectedRow = dataGridViewUsers.SelectedRows[0];
            _currentUser = selectedRow.DataBoundItem as User;
            if (_currentUser != null)
            {
                PopulateUserFields(_currentUser);
            }
        }
    }

    private User GetUserFromInput()
    {
        return new User
        {
            UserID = textBoxUserID.Text,
            Username = textBoxUsername.Text,
            PasswordHash = textBoxPasswordHash.Text,
            usrPicture = null, // Handle image upload separately if needed
            Role = textBoxRole.Text,
            isActive = checkBoxIsActive.Checked,
            CreatedBy = "System", // Replace with actual user or value
            UpdatedBy = "System"  // Replace with actual user or value
        };
    }

    private void PopulateUserFields(User user)
    {
        textBoxUserID.Text = user.UserID;
        textBoxUsername.Text = user.Username;
        textBoxPasswordHash.Text = user.PasswordHash;
        textBoxRole.Text = user.Role;
        checkBoxIsActive.Checked = user.isActive;
    }
}
```

#### UI Design (UserForm)

1. Add a `DataGridView` named `dataGridViewUsers` to display the user list.
2. Add `TextBox` controls for `UserID`, `Username`, `PasswordHash`, `Role`.
3. Add a `CheckBox` for `isActive`.
4. Add `Button` controls for `Add`, `Update`, `Delete`.
5. Add event handlers for the button clicks and `SelectionChanged` event for the `DataGridView`.

This is a basic structure. Depending on your needs, you might want to add more validations, error handling, and improve the UI/UX.