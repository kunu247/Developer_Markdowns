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

### SQL Table Structures

-- 1. Customers Table
   CREATE TABLE Customers (
       CustomerID INT IDENTITY(1,1) PRIMARY KEY,
       cName NVARCHAR(100) NOT NULL,
       cPhone NVARCHAR(15),
       cEmail NVARCHAR(100),
       cAddress NVARCHAR(255),
       cRegistrationDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

-- 2. FoodTypes Table
   CREATE TABLE FoodTypes (
       FoodTypeID INT IDENTITY(1,1) PRIMARY KEY,
       Name NVARCHAR(100) NOT NULL,
       Description NVARCHAR(255),
       Price DECIMAL(10, 2) NOT NULL,
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

-- 3. CoWorkers Table
   CREATE TABLE CoWorkers (
       CoWorkerID INT IDENTITY(1,1) PRIMARY KEY,
       cwName NVARCHAR(100) NOT NULL,
       cwRole NVARCHAR(50),
       cwPhone NVARCHAR(15),
       cwAddress NVARCHAR(255),
       cwHireDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

-- 4. Ingredients Table
   CREATE TABLE Ingredients (
       ID INT IDENTITY(1,1) PRIMARY KEY,
	   IID VARCHAR(10) UNIQUE NOT NULL,
       IngrediantName NVARCHAR(100) NOT NULL,
       Quantity NVARCHAR(50),
       UnitPrice DECIMAL(10, 2) NOT NULL,
       PurchaseDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

-- 5. Incomes Table
   CREATE TABLE Incomes (
       IncomeID INT IDENTITY(1,1) PRIMARY KEY,
       CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
       FoodTypeID INT FOREIGN KEY REFERENCES FoodTypes(FoodTypeID),
       Quantity INT NOT NULL,
       TotalAmount DECIMAL(10, 2) NOT NULL,
       IncomeDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

-- 6. Expenditures Table
   CREATE TABLE Expenditures (
       ExpenditureID INT IDENTITY(1,1) PRIMARY KEY,
       IngredientID INT FOREIGN KEY REFERENCES Ingredients(ID),
       Quantity INT NOT NULL,
       TotalCost DECIMAL(10, 2) NOT NULL,
       ExpenditureDate DATETIME DEFAULT GETDATE(),
	   CreatedBy NVARCHAR(100) NULL,
	   CreatedOn DATETIME DEFAULT GETDATE(),
	   UpdatedBy NVARCHAR(100) NULL,
	   UpdatedOn DATETIME NULL
   );

### Stored Procedures

1. **Insert Customer**
   ```sql
   CREATE PROCEDURE InsertCustomer
       @Name NVARCHAR(100),
       @PhoneNumber NVARCHAR(15),
       @Email NVARCHAR(100),
       @Address NVARCHAR(255)
   AS
   BEGIN
       INSERT INTO Customers (Name, PhoneNumber, Email, Address)
       VALUES (@Name, @PhoneNumber, @Email, @Address);
   END;
   ```

2. **Insert Food Type**
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

4. **Insert Ingredient**
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

5. **Insert Income**
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

6. **Insert Expenditure**
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

7. **Calculate Sales and Purchases**
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