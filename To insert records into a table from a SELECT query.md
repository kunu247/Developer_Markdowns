To insert records into a table from a `SELECT` query, you can use the `INSERT INTO ... SELECT` statement in SQL. Here’s the general syntax:

```sql
INSERT INTO target_table (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM source_table
WHERE condition;
```

### Example
Assume you have two tables, `Students` and `ArchivedStudents`. You want to copy records from `Students` to `ArchivedStudents` where the `Age` is greater than 20.

```sql
INSERT INTO ArchivedStudents (Id, Name, UserName, Email, Age)
SELECT Id, Name, UserName, Email, Age
FROM Students
WHERE Age > 20;
```

This query will insert the selected records from the `Students` table into the `ArchivedStudents` table. Make sure the columns in both tables match in type and order.