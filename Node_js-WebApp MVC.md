To create a CRUD app based on your provided `Users` table structure using **Express**, **EJS**, **MS SQL Server**, and **Node.js**, I will revise the code to handle the additional fields (`usrPicture`, `Role`, `isActive`, etc.) and ensure compatibility with your `Users` table.

### Table Structure:

```sql
SELECT [ID]
      ,[UserID]
      ,[Username]
      ,[PasswordHash]
      ,[usrPicture]
      ,[Role]
      ,[isActive]
      ,[CreatedBy]
      ,[CreationDate]
      ,[UpdatedBy]
      ,[UpdationDate]
  FROM [DevShrishDB].[dbo].[Users]
```

### Required Folder Structure:
```
DeveloperData/
│
├── app.js
├── package-lock.json
├── package.json
│
├───bin/
│   └── www
│
├───model/
│   └── UserModel.js
│
├───node_modules/
│
├───public/
│   ├───images/
│   ├───javascripts/
│   └───stylesheets/
│       └── global-styles.css
│       └── style.css
│
├───routes/
│   └── index.js
│   └── users.js
│
├───util/
│   └── DbConnections.js
│
├───views/
│   └── error.ejs
│   └── index.ejs
│   └── users/
│       └── create.ejs
│       └── edit.ejs
│       └── list.ejs
│       └── view.ejs
│
└───controllers/
    └── userController.js
```

### Revised Files:

#### 1. **`app.js`**
This is the entry point for your app, connecting routes and handling middleware.

```javascript
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const db = require('./util/DbConnections');
const indexRouter = require('./routes/index');
const usersRouter = require('./routes/users');

const app = express();

// Set view engine to EJS
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

// Middleware
app.use(express.static(path.join(__dirname, 'public')));
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// Routes
app.use('/', indexRouter);
app.use('/users', usersRouter);

module.exports = app;
```

#### 2. **`model/UserModel.js`**
This file will handle SQL queries for your `Users` table and the necessary CRUD operations.

```javascript
const sql = require('mssql');
const db = require('../util/DbConnections');

// Get all users
exports.getAllUsers = async () => {
    const result = await db.query('SELECT * FROM Users');
    return result.recordset;
};

// Get user by ID
exports.getUserById = async (id) => {
    const result = await db.query('SELECT * FROM Users WHERE ID = @id', { id });
    return result.recordset[0];
};

// Create a new user
exports.createUser = async (userData) => {
    const { UserID, Username, PasswordHash, usrPicture, Role, isActive, CreatedBy, CreationDate } = userData;
    await db.query(`
        INSERT INTO Users (UserID, Username, PasswordHash, usrPicture, Role, isActive, CreatedBy, CreationDate) 
        VALUES (@UserID, @Username, @PasswordHash, @usrPicture, @Role, @isActive, @CreatedBy, @CreationDate)`,
        { UserID, Username, PasswordHash, usrPicture, Role, isActive, CreatedBy, CreationDate });
};

// Update a user
exports.updateUser = async (id, userData) => {
    const { Username, PasswordHash, usrPicture, Role, isActive, UpdatedBy, UpdationDate } = userData;
    await db.query(`
        UPDATE Users 
        SET Username = @Username, PasswordHash = @PasswordHash, usrPicture = @usrPicture, Role = @Role, 
            isActive = @isActive, UpdatedBy = @UpdatedBy, UpdationDate = @UpdationDate 
        WHERE ID = @id`, 
        { id, Username, PasswordHash, usrPicture, Role, isActive, UpdatedBy, UpdationDate });
};

// Delete a user
exports.deleteUser = async (id) => {
    await db.query('DELETE FROM Users WHERE ID = @id', { id });
};
```

#### 3. **`routes/users.js`**
This file defines all the routes for handling CRUD operations for `Users`.

```javascript
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

// Routes for user CRUD
router.get('/', userController.listUsers);          // List all users
router.get('/create', userController.createForm);   // Show create form
router.post('/create', userController.createUser);  // Handle create form submission
router.get('/edit/:id', userController.editForm);   // Show edit form
router.post('/edit/:id', userController.updateUser);// Handle edit form submission
router.get('/delete/:id', userController.deleteUser);// Handle user deletion

module.exports = router;
```

#### 4. **`controllers/userController.js`**
This file defines the controller logic for CRUD operations.

```javascript
const UserModel = require('../model/UserModel');

// Display list of users
exports.listUsers = async (req, res) => {
    const users = await UserModel.getAllUsers();
    res.render('users/list', { users });
};

// Display form to create a new user
exports.createForm = (req, res) => {
    res.render('users/create');
};

// Create a new user
exports.createUser = async (req, res) => {
    const userData = {
        UserID: req.body.UserID,
        Username: req.body.Username,
        PasswordHash: req.body.PasswordHash,
        usrPicture: req.body.usrPicture, // Assuming base64 or file URL
        Role: req.body.Role,
        isActive: req.body.isActive,
        CreatedBy: req.body.CreatedBy,
        CreationDate: new Date()
    };
    await UserModel.createUser(userData);
    res.redirect('/users');
};

// Display form to edit a user
exports.editForm = async (req, res) => {
    const user = await UserModel.getUserById(req.params.id);
    res.render('users/edit', { user });
};

// Update user
exports.updateUser = async (req, res) => {
    const userData = {
        Username: req.body.Username,
        PasswordHash: req.body.PasswordHash,
        usrPicture: req.body.usrPicture,  // Assuming base64 or file URL
        Role: req.body.Role,
        isActive: req.body.isActive,
        UpdatedBy: req.body.UpdatedBy,
        UpdationDate: new Date()
    };
    await UserModel.updateUser(req.params.id, userData);
    res.redirect('/users');
};

// Delete a user
exports.deleteUser = async (req, res) => {
    await UserModel.deleteUser(req.params.id);
    res.redirect('/users');
};
```

#### 5. **`views/users/list.ejs`**
Displays a list of users with options to view, edit, and delete.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>User List</title>
    <link rel="stylesheet" href="/stylesheets/style.css">
</head>
<body>
    <h1>User List</h1>
    <a href="/users/create">Create New User</a>
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>UserID</th>
                <th>Username</th>
                <th>Role</th>
                <th>isActive</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            <% users.forEach(user => { %>
                <tr>
                    <td><%= user.ID %></td>
                    <td><%= user.UserID %></td>
                    <td><%= user.Username %></td>
                    <td><%= user.Role %></td>
                    <td><%= user.isActive %></td>
                    <td>
                        <a href="/users/edit/<%= user.ID %>">Edit</a> |
                        <a href="/users/delete/<%= user.ID %>">Delete</a>
                    </td>
                </tr>
            <% }) %>
        </tbody>
    </table>
</body>
</html>
```

#### 6. **`util/DbConnections.js`**
This file manages the database connection using the `mssql` package.

```javascript
const sql = require('mssql');

// DB Connection configuration
const config = {
    user: 'your_username',
    password: 'your_password',
    server: 'your_server',
    database: 'DevShrishDB',
    options: {
        encrypt: true,  // Required for Azure
        enableArithAbort: true
    }
};

// Create connection pool
const pool = new sql.ConnectionPool(config);
const poolConnect = pool.connect();

pool.on('error', err => {
    console.error('Database connection failed:', err);
});

exports.query = async (query, params = {}) => {
    await poolConnect;
    const request = pool.request();
    for (const key in params) {
        request

.input(key, params[key]);
    }
    return await request.query(query);
};
```

### To Run:
1. **Install dependencies**:
   ```
   npm install express ejs body-parser mssql
   ```

2. **Start the application**:
   ```
   npm start
   ```

This setup provides full CRUD functionality for the `Users` table, including the creation, reading, updating, and deletion of records from your MS SQL Server. You can customize this further to fit your needs.