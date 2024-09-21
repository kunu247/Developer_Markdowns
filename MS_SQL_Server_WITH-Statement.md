The `WITH` statement in SQL Server is commonly used to create a **Common Table Expression (CTE)**. A CTE is a temporary result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` query. It's particularly useful when you want to simplify complex queries, make them more readable, or avoid repeating the same subquery multiple times.

### Key Features of a CTE (`WITH` statement):
1. **Temporary Result Set**: CTEs exist only within the execution of the query they are part of. They are not stored in the database.
2. **Recursive Queries**: CTEs can be used to perform recursive operations, like traversing a hierarchy.
3. **Improved Readability**: By breaking down a complex query into parts, CTEs help in writing clearer code.

### Basic Syntax:

```sql
WITH CTE_Name (Column1, Column2, ...)
AS
(
    -- Your query here
)
SELECT * FROM CTE_Name;
```

- **CTE_Name**: The name you give to your CTE.
- **Column1, Column2, ...**: (Optional) The column names in the CTE. If you don’t specify these, SQL Server will take the names from the columns in the inner query.
- **Query**: The actual query whose result will be stored temporarily in the CTE.

### Example 1: Simplifying a Query
Let's say you have a table `Employees` with columns `EmployeeID`, `FirstName`, `LastName`, and `DepartmentID`. You want to get a list of all employees along with the name of the department they belong to, from the `Departments` table.

**Without CTE** (using a subquery):
```sql
SELECT e.EmployeeID, e.FirstName, e.LastName, d.DepartmentName
FROM Employees e
JOIN (SELECT DepartmentID, DepartmentName FROM Departments) d
ON e.DepartmentID = d.DepartmentID;
```

**With CTE**:
```sql
WITH DepartmentCTE AS
(
    SELECT DepartmentID, DepartmentName FROM Departments
)
SELECT e.EmployeeID, e.FirstName, e.LastName, d.DepartmentName
FROM Employees e
JOIN DepartmentCTE d ON e.DepartmentID = d.DepartmentID;
```

- Here, the CTE `DepartmentCTE` holds the result of the `Departments` table, which simplifies the main query.

### Example 2: Recursive CTE
CTEs are very useful for recursive queries, like when you have hierarchical data (e.g., employee-manager relationships) and need to query it.

Suppose you have a table `Employees` with columns `EmployeeID`, `ManagerID`, and `EmployeeName`. The `ManagerID` refers to the `EmployeeID` of the employee’s manager.

You want to find the hierarchy under a particular manager.

```sql
WITH EmployeeHierarchy AS
(
    SELECT EmployeeID, EmployeeName, ManagerID
    FROM Employees
    WHERE ManagerID IS NULL -- Starting point (top manager)
    
    UNION ALL
    
    SELECT e.EmployeeID, e.EmployeeName, e.ManagerID
    FROM Employees e
    INNER JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT * FROM EmployeeHierarchy;
```

- The `EmployeeHierarchy` CTE references itself to recursively fetch employees and their subordinates.

### Example 3: Using CTE for Ranking
CTEs can be used in conjunction with window functions to rank rows.

Suppose you want to rank employees by their salary within each department.

```sql
WITH RankedEmployees AS
(
    SELECT EmployeeID, EmployeeName, Salary, DepartmentID,
           RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS RankInDept
    FROM Employees
)
SELECT * FROM RankedEmployees WHERE RankInDept = 1; -- Highest paid employee per department
```

### Advantages of CTEs:
1. **Clarity**: CTEs improve readability, especially for complex queries that might involve multiple subqueries or temporary calculations.
2. **Reusability**: A CTE can be referred to multiple times in the main query, avoiding repetition.
3. **Recursion**: CTEs support recursion, which is useful for querying hierarchical data.
4. **Scoped for One Query**: Unlike temporary tables, CTEs are defined for the scope of a single query, making them lightweight.

### Limitations:
- **Performance**: CTEs are not indexed and may not always perform as well as other techniques like temp tables, especially with large datasets.
- **Scope**: CTEs exist only for the duration of the query. They cannot be reused across different queries without redefining them.

### When to Use CTEs:
- To **simplify complex queries** that involve multiple joins or subqueries.
- When working with **recursive data**, like employee hierarchies.
- To **avoid repeating subqueries** by defining them once in a CTE and reusing them.

### Example 4: Using Multiple CTEs
You can define multiple CTEs in a single query by separating them with commas.

```sql
WITH DeptCTE AS
(
    SELECT DepartmentID, DepartmentName FROM Departments
),
EmpCTE AS
(
    SELECT EmployeeID, EmployeeName, DepartmentID FROM Employees
)
SELECT e.EmployeeID, e.EmployeeName, d.DepartmentName
FROM EmpCTE e
JOIN DeptCTE d ON e.DepartmentID = d.DepartmentID;
```

### Conclusion:
The `WITH` statement in SQL Server, used to define CTEs, is a powerful tool to make queries more readable and maintainable. It's particularly helpful in recursive queries and when breaking down complex SQL logic. However, for very large datasets or performance-critical applications, you may need to evaluate whether a CTE or an alternative approach (like temporary tables or indexed views) is more appropriate.