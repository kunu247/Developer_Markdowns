To set up a **Node.js web application** that connects to **MS SQL Server** using the `.env`, `mssql`, and `express` packages, here’s a step-by-step guide. This includes creating a **User model**, **User view** (using EJS), **User controller** for CRUD operations, and **routes** for managing users.

### **Project Structure:**

Your project directory should look like this:

```
InventoryApp/
│
├── config/
│   └── db.js                # DB configuration
├── controllers/
│   └── userController.js     # User controller for CRUD operations
├── middlewares/
│   └── errorHandler.js       # Error handling middleware
├── models/
│   └── User.js               # User model
├── routes/
│   └── userRoutes.js         # User routes
├── views/
│   ├── user/
│   │   └── create.ejs        # Create user view
│   │   └── edit.ejs          # Edit user view
│   │   └── list.ejs          # List users view
│   └── partials/
│       └── header.ejs        # Header template
├── .env                      # Environment variables
├── app.js                    # Main entry point
├── package.json              # Dependencies
└── README.md
```

### **1. Install Required Packages:**

First, you need to install the required packages for Node.js.

```bash
npm init -y
npm install express dotenv mssql ejs body-parser
```

- **express**: Web framework
- **dotenv**: To load environment variables from `.env`
- **mssql**: MS SQL Server driver for Node.js
- **ejs**: View engine
- **body-parser**: Middleware for parsing incoming request bodies

### **2. Create `.env` File:**

Your `.env` file will store sensitive data like the SQL Server credentials.

```env
DB_SERVER=localhost
DB_USER=your_db_username
DB_PASSWORD=your_db_password
DB_NAME=your_db_name
PORT=3000
```

### **3. Create `db.js` for Database Configuration:**

In `config/db.js`, set up the connection to MS SQL Server using the `mssql` package and environment variables.

```javascript
require('dotenv').config();
const sql = require('mssql');

// Database configuration
const dbConfig = {
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    server: process.env.DB_SERVER, // Replace with your server name
    database: process.env.DB_NAME,
    options: {
        encrypt: false, // Use true if you're using Azure SQL
        enableArithAbort: true,
    }
};

// Connect to the database
const connectDB = async () => {
    try {
        await sql.connect(dbConfig);
        console.log("Connected to SQL Server successfully.");
    } catch (err) {
        console.error("Database connection failed:", err.message);
    }
};

module.exports = { sql, connectDB };
```

### **4. Create the `User.js` Model:**

In `models/User.js`, define the **User** model for interacting with the database.

```javascript
const { sql } = require('../config/db');

class User {
    static async getAll() {
        const request = new sql.Request();
        return request.query('SELECT * FROM Users');
    }

    static async getById(id) {
        const request = new sql.Request();
        request.input('id', sql.Int, id);
        return request.query('SELECT * FROM Users WHERE id = @id');
    }

    static async create(data) {
        const request = new sql.Request();
        request.input('username', sql.VarChar, data.username);
        request.input('email', sql.VarChar, data.email);
        return request.query('INSERT INTO Users (username, email) VALUES (@username, @email)');
    }

    static async update(id, data) {
        const request = new sql.Request();
        request.input('id', sql.Int, id);
        request.input('username', sql.VarChar, data.username);
        request.input('email', sql.VarChar, data.email);
        return request.query('UPDATE Users SET username = @username, email = @email WHERE id = @id');
    }

    static async delete(id) {
        const request = new sql.Request();
        request.input('id', sql.Int, id);
        return request.query('DELETE FROM Users WHERE id = @id');
    }
}

module.exports = User;
```

### **5. Create the `userController.js`:**

In `controllers/userController.js`, write the logic for handling CRUD operations.

```javascript
const User = require('../models/User');

// Get all users
exports.getAllUsers = async (req, res) => {
    try {
        const result = await User.getAll();
        res.render('user/list', { users: result.recordset });
    } catch (err) {
        res.status(500).send(err.message);
    }
};

// Get user by ID
exports.getUserById = async (req, res) => {
    try {
        const result = await User.getById(req.params.id);
        res.render('user/edit', { user: result.recordset[0] });
    } catch (err) {
        res.status(500).send(err.message);
    }
};

// Create new user
exports.createUser = async (req, res) => {
    try {
        await User.create(req.body);
        res.redirect('/users');
    } catch (err) {
        res.status(500).send(err.message);
    }
};

// Update user
exports.updateUser = async (req, res) => {
    try {
        await User.update(req.params.id, req.body);
        res.redirect('/users');
    } catch (err) {
        res.status(500).send(err.message);
    }
};

// Delete user
exports.deleteUser = async (req, res) => {
    try {
        await User.delete(req.params.id);
        res.redirect('/users');
    } catch (err) {
        res.status(500).send(err.message);
    }
};
```

### **6. Create Routes for Users:**

In `routes/userRoutes.js`, define the routes for user-related actions.

```javascript
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

// List all users
router.get('/', userController.getAllUsers);

// Create user form
router.get('/create', (req, res) => {
    res.render('user/create');
});

// Create user
router.post('/create', userController.createUser);

// Edit user form
router.get('/:id/edit', userController.getUserById);

// Update user
router.post('/:id/edit', userController.updateUser);

// Delete user
router.post('/:id/delete', userController.deleteUser);

module.exports = router;
```

### **7. Create Views for Users (EJS):**

In `views/user/`, create EJS templates for CRUD views.

**`list.ejs`** (List users):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>User List</title>
</head>
<body>
    <h1>All Users</h1>
    <a href="/users/create">Create New User</a>
    <ul>
        <% users.forEach(user => { %>
            <li>
                <%= user.username %> (<%= user.email %>)
                <a href="/users/<%= user.id %>/edit">Edit</a>
                <form action="/users/<%= user.id %>/delete" method="POST" style="display:inline;">
                    <button type="submit">Delete</button>
                </form>
            </li>
        <% }) %>
    </ul>
</body>
</html>
```

**`create.ejs`** (Create user form):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Create User</title>
</head>
<body>
    <h1>Create User</h1>
    <form action="/users/create" method="POST">
        <input type="text" name="username" placeholder="Username" required />
        <input type="email" name="email" placeholder="Email" required />
        <button type="submit">Create</button>
    </form>
</body>
</html>
```

**`edit.ejs`** (Edit user form):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Edit User</title>
</head>
<body>
    <h1>Edit User</h1>
    <form action="/users/<%= user.id %>/edit" method="POST">
        <input type="text" name="username" value="<%= user.username %>" required />
        <input type="email" name="email" value="<%= user.email %>" required />
        <button type="submit">Update</button>
    </form>
</body>
</html>
```

### **8. Set Up Middlewares:**

In `middlewares/errorHandler.js`, you can handle any errors globally.

```javascript
module.exports = (err, req, res, next) => {
    console.error(err.stack);
    res.status(500).send('Something went wrong!');
};
```

### **9. Configure the Main Application File (`app.js`):**

Now, in `app.js`, initialize the Express app, load the routes, and connect to the database.

```javascript
const express = require('express');
const bodyParser = require('body-parser');
const { connectDB } = require('./config/db');
const userRoutes = require('./routes/userRoutes');
const errorHandler = require('./middlewares

/errorHandler');
require('dotenv').config();

const app = express();

// Connect to the database
connectDB();

// Set EJS as view engine
app.set('view engine', 'ejs');

// Middlewares
app.use(bodyParser.urlencoded({ extended: false }));

// Routes
app.use('/users', userRoutes);

// Error handling middleware
app.use(errorHandler);

// Start the server
const port = process.env.PORT || 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

### **10. Run the Application:**

To start your application, run the following command:

```bash
node app.js
```

Your Node.js application should now be running on `http://localhost:3000/users`. You can perform CRUD operations on the `Users` table from your MS SQL Server.


