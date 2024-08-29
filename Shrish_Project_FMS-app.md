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

1. **Customers Table**
   ```sql
   CREATE TABLE Customers (
       CustomerID INT IDENTITY(1,1) PRIMARY KEY,
       Name NVARCHAR(100) NOT NULL,
       PhoneNumber NVARCHAR(15),
       Email NVARCHAR(100),
       Address NVARCHAR(255),
       RegistrationDate DATETIME DEFAULT GETDATE()
   );
   ```

2. **FoodTypes Table**
   ```sql
   CREATE TABLE FoodTypes (
       FoodTypeID INT IDENTITY(1,1) PRIMARY KEY,
       Name NVARCHAR(100) NOT NULL,
       Description NVARCHAR(255),
       Price DECIMAL(10, 2) NOT NULL,
       CreatedAt DATETIME DEFAULT GETDATE(),
       UpdatedAt DATETIME
   );
   ```

3. **CoWorkers Table**
   ```sql
   CREATE TABLE CoWorkers (
       CoWorkerID INT IDENTITY(1,1) PRIMARY KEY,
       Name NVARCHAR(100) NOT NULL,
       Role NVARCHAR(50),
       PhoneNumber NVARCHAR(15),
       Address NVARCHAR(255),
       HireDate DATETIME DEFAULT GETDATE()
   );
   ```

4. **Ingredients Table**
   ```sql
   CREATE TABLE Ingredients (
       IngredientID INT IDENTITY(1,1) PRIMARY KEY,
       Name NVARCHAR(100) NOT NULL,
       Quantity NVARCHAR(50),
       UnitPrice DECIMAL(10, 2) NOT NULL,
       PurchaseDate DATETIME DEFAULT GETDATE()
   );
   ```

5. **Incomes Table**
   ```sql
   CREATE TABLE Incomes (
       IncomeID INT IDENTITY(1,1) PRIMARY KEY,
       CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
       FoodTypeID INT FOREIGN KEY REFERENCES FoodTypes(FoodTypeID),
       Quantity INT NOT NULL,
       TotalAmount DECIMAL(10, 2) NOT NULL,
       IncomeDate DATETIME DEFAULT GETDATE()
   );
   ```

6. **Expenditures Table**
   ```sql
   CREATE TABLE Expenditures (
       ExpenditureID INT IDENTITY(1,1) PRIMARY KEY,
       IngredientID INT FOREIGN KEY REFERENCES Ingredients(IngredientID),
       Quantity INT NOT NULL,
       TotalCost DECIMAL(10, 2) NOT NULL,
       ExpenditureDate DATETIME DEFAULT GETDATE()
   );
   ```

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