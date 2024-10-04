# Comprehensive EJS (Embedded JavaScript) View Engine Tutorial

EJS (Embedded JavaScript) is a simple yet powerful templating language that integrates seamlessly with Node.js and Express.js applications. It allows developers to generate HTML markup with plain JavaScript, making it an excellent choice for server-side rendering. This tutorial provides an in-depth exploration of EJS, covering its installation, usage, syntax, advanced features, and practical examples.

---

## Table of Contents

1. [Introduction to EJS](#introduction-to-ejs)
2. [Setting Up Your Environment](#setting-up-your-environment)
3. [Basic Usage of EJS](#basic-usage-of-ejs)
4. [EJS Syntax and Features](#ejs-syntax-and-features)
    - [Variables](#variables)
    - [Control Flow](#control-flow)
    - [Including Partials](#including-partials)
    - [Layouts](#layouts)
    - [Filters and Custom Tags](#filters-and-custom-tags)
5. [Advanced EJS Features](#advanced-ejs-features)
    - [Asynchronous Includes](#asynchronous-includes)
    - [Custom Delimiters](#custom-delimiters)
6. [Integration with Express.js](#integration-with-expressjs)
7. [Practical Examples](#practical-examples)
    - [Example 1: Simple Webpage](#example-1-simple-webpage)
    - [Example 2: Dynamic Data Rendering](#example-2-dynamic-data-rendering)
    - [Example 3: Using Partials and Layouts](#example-3-using-partials-and-layouts)
8. [Best Practices](#best-practices)
9. [Troubleshooting Common Issues](#troubleshooting-common-issues)
10. [Conclusion](#conclusion)

---

## Introduction to EJS

**EJS (Embedded JavaScript)** is a templating language that lets you generate HTML markup with plain JavaScript. It’s designed to be simple and easy to use, making it a popular choice for server-side rendering in Node.js applications, especially with Express.js.

**Key Features:**

- **Simplicity:** Easy to learn and integrate.
- **Flexibility:** Allows embedding JavaScript logic within templates.
- **Performance:** Fast rendering of templates.
- **Compatibility:** Works seamlessly with Express.js and other Node.js frameworks.

---

## Setting Up Your Environment

To start using EJS, you need to set up a Node.js environment. Here’s how:

1. **Install Node.js:** If you haven’t installed Node.js, download it from [nodejs.org](https://nodejs.org/) and follow the installation instructions for your operating system.

2. **Initialize a New Project:**
   ```bash
   mkdir ejs-tutorial
   cd ejs-tutorial
   npm init -y
   ```

3. **Install EJS:**
   ```bash
   npm install ejs
   ```

4. **Optional - Install Express.js:**
   While EJS can be used standalone, it’s commonly paired with Express.js for building web applications.
   ```bash
   npm install express
   ```

---

## Basic Usage of EJS

EJS can be used to render HTML pages with dynamic data. Here's a simple example to illustrate its usage.

### Creating a Simple EJS Template

1. **Create a `views` Directory:**
   ```bash
   mkdir views
   ```

2. **Create an `index.ejs` File in `views`:**
   ```html
   <!-- views/index.ejs -->
   <!DOCTYPE html>
   <html>
   <head>
       <title><%= title %></title>
   </head>
   <body>
       <h1>Hello, <%= user %>!</h1>
   </body>
   </html>
   ```

### Rendering the Template

If you're using Express.js, set up a basic server to render this template.

```javascript
// app.js
const express = require('express');
const app = express();

// Set EJS as the templating engine
app.set('view engine', 'ejs');

// Define a route
app.get('/', (req, res) => {
    res.render('index', { title: 'Welcome Page', user: 'John Doe' });
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
```

**Run the Server:**
```bash
node app.js
```

Navigate to `http://localhost:3000/` in your browser to see the rendered HTML.

---

## EJS Syntax and Features

EJS provides several features to embed JavaScript within your HTML templates. Understanding these features is crucial for building dynamic web pages.

### Variables

Variables are used to inject dynamic data into your templates.

```html
<h1>Welcome, <%= username %>!</h1>
```

- `<%= %>`: Outputs the value into the template (escaped).

**Example:**

```javascript
res.render('profile', { username: 'Alice' });
```

**Rendered HTML:**

```html
<h1>Welcome, Alice!</h1>
```

### Control Flow

EJS allows you to use JavaScript control structures like conditionals and loops directly within your templates.

#### Conditionals

```html
<% if (user) { %>
    <h1>Welcome, <%= user.name %>!</h1>
<% } else { %>
    <h1>Welcome, Guest!</h1>
<% } %>
```

#### Loops

**For Loop:**

```html
<ul>
    <% for(let i = 0; i < items.length; i++) { %>
        <li><%= items[i] %></li>
    <% } %>
</ul>
```

**ForEach Loop:**

```html
<ul>
    <% items.forEach(function(item) { %>
        <li><%= item %></li>
    <% }); %>
</ul>
```

### Including Partials

Partials are reusable snippets of code that can be included in multiple templates, such as headers, footers, or navigation bars.

**Create a Partial:**

```html
<!-- views/partials/header.ejs -->
<header>
    <h1><%= title %></h1>
</header>
```

**Include the Partial in a Template:**

```html
<!-- views/index.ejs -->
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <% include partials/header %>
    <p>Welcome to the homepage.</p>
</body>
</html>
```

### Layouts

While EJS doesn't have built-in layout support, you can simulate layouts using partials or by creating a base template.

**Example Using Partials for Layouts:**

1. **Create a Base Layout:**

```html
<!-- views/layout.ejs -->
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <% include partials/header %>
    <div class="content">
        <%- body %>
    </div>
    <% include partials/footer %>
</body>
</html>
```

2. **Render with Layout:**

```javascript
const express = require('express');
const app = express();

// Set EJS as the templating engine
app.set('view engine', 'ejs');

// Middleware to render with layout
app.use((req, res, next) => {
    res.renderWithLayout = (view, options) => {
        res.render(view, { ...options, layout: 'layout' }, (err, html) => {
            if (err) throw err;
            res.send(html);
        });
    };
    next();
});

// Define a route
app.get('/', (req, res) => {
    res.renderWithLayout('index', { title: 'Home Page' });
});

// Start the server
app.listen(3000);
```

**Note:** For more sophisticated layout management, consider using additional middleware or switch to a templating engine with built-in layout support.

### Filters and Custom Tags

EJS doesn’t natively support filters or custom tags, but you can extend its functionality by preprocessing data or using helper functions.

---

## Advanced EJS Features

### Asynchronous Includes

By default, EJS includes are synchronous. However, you can perform asynchronous operations within your templates by using async functions and Promises.

**Example:**

```javascript
// Assuming you have async data fetching functions
app.get('/data', async (req, res) => {
    const data = await fetchData();
    res.render('data', { data });
});
```

Within the EJS template, you can handle data once it's fetched.

### Custom Delimiters

EJS allows you to change its default delimiters if needed, which can be useful to avoid conflicts with other templating languages.

**Example:**

```javascript
app.set('delimiter', '?'); // Changes <% to <?, etc.
```

In your templates:

```html
<? if (user) { ?>
    <h1>Welcome, <?= user.name ?>!</h1>
<? } else { ?>
    <h1>Welcome, Guest!</h1>
<? } ?>
```

---

## Integration with Express.js

EJS integrates seamlessly with Express.js, one of the most popular Node.js web frameworks.

### Setting Up Express with EJS

1. **Install Express:**
   ```bash
   npm install express
   ```

2. **Configure EJS in Express:**

```javascript
const express = require('express');
const app = express();

// Set EJS as the templating engine
app.set('view engine', 'ejs');

// Define a route
app.get('/', (req, res) => {
    res.render('index', { title: 'Home', user: 'John' });
});

// Start the server
app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
```

### Using Middleware

Express allows you to use middleware to handle various aspects of your application, such as serving static files or parsing request bodies.

**Example: Serving Static Files:**

```javascript
app.use(express.static('public'));
```

---

## Practical Examples

Let's walk through some practical examples to solidify your understanding of EJS.

### Example 1: Simple Webpage

**Objective:** Render a static webpage with EJS.

**Template (`views/index.ejs`):**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Simple EJS Page</title>
</head>
<body>
    <h1>Welcome to EJS Tutorial</h1>
    <p>This is a simple static page rendered using EJS.</p>
</body>
</html>
```

**Server (`app.js`):**

```javascript
const express = require('express');
const app = express();

app.set('view engine', 'ejs');

app.get('/', (req, res) => {
    res.render('index');
});

app.listen(3000);
```

**Result:** Accessing `http://localhost:3000/` displays the static content.

### Example 2: Dynamic Data Rendering

**Objective:** Render dynamic user data on the page.

**Template (`views/user.ejs`):**

```html
<!DOCTYPE html>
<html>
<head>
    <title>User Profile</title>
</head>
<body>
    <h1>Profile of <%= user.name %></h1>
    <p>Age: <%= user.age %></p>
    <p>Email: <%= user.email %></p>
</body>
</html>
```

**Server (`app.js`):**

```javascript
app.get('/user', (req, res) => {
    const user = {
        name: 'Alice',
        age: 25,
        email: 'alice@example.com'
    };
    res.render('user', { user });
});
```

**Result:** Accessing `http://localhost:3000/user` displays Alice's profile.

### Example 3: Using Partials and Layouts

**Objective:** Create a layout with header and footer partials.

**Header Partial (`views/partials/header.ejs`):**

```html
<header>
    <h1><%= title %></h1>
    <nav>
        <a href="/">Home</a> |
        <a href="/about">About</a>
    </nav>
</header>
```

**Footer Partial (`views/partials/footer.ejs`):**

```html
<footer>
    <p>&copy; 2024 EJS Tutorial</p>
</footer>
```

**Layout Template (`views/layout.ejs`):**

```html
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <% include partials/header %>
    <div class="content">
        <%- body %>
    </div>
    <% include partials/footer %>
</body>
</html>
```

**Page Template (`views/about.ejs`):**

```html
<h2>About Us</h2>
<p>This is the about page.</p>
```

**Server (`app.js`):**

To implement layouts, you can use a helper like `express-ejs-layouts` or manually render the layout with embedded content. Here's a manual approach:

```javascript
app.get('/about', (req, res) => {
    res.render('layout', {
        title: 'About Us',
        body: '<h2>About Us</h2><p>This is the about page.</p>'
    });
});
```

**Result:** Accessing `http://localhost:3000/about` displays the about page within the layout.

---

## Best Practices

1. **Use Partials for Reusability:** Break down your templates into reusable components like headers, footers, and navigation bars.

2. **Escape Output:** Use `<%= %>` to escape HTML entities and prevent XSS attacks. Use `<%- %>` to render unescaped content when necessary.

3. **Organize Views:** Structure your `views` directory logically, grouping related templates together.

4. **Minimize Logic in Templates:** Keep business logic out of your templates. Use them primarily for presentation.

5. **Use Layouts Wisely:** Implement layouts to maintain consistency across pages, reducing redundancy.

6. **Leverage Template Inheritance:** While EJS doesn't support it natively, simulate it using partials or helper functions for cleaner templates.

7. **Optimize Performance:** Cache rendered templates if possible and avoid unnecessary computations within templates.

---

## Troubleshooting Common Issues

### 1. **Template Not Found**

**Error Message:**
```
Error: Failed to lookup view "index" in views directory
```

**Solution:**
- Ensure the `views` directory exists.
- Verify the template file (`index.ejs`) is in the correct directory.
- Check the `view engine` is set to `ejs` in your Express configuration.

### 2. **Syntax Errors in EJS Templates**

**Error Message:**
```
Error: Parse error on line X: ... <%= user.name %> ...
```

**Solution:**
- Review your EJS syntax for missing or extra tags.
- Ensure all JavaScript code within `<% %>` is valid.

### 3. **Variables Not Displaying Correctly**

**Issue:**
Variables show as `undefined` or don't display.

**Solution:**
- Verify that the variable is passed correctly from the server.
- Check the spelling and casing of variable names.
- Ensure the variable exists and has a value before rendering.

### 4. **Including Partials Fails**

**Error Message:**
```
Error: Failed to include partial "partials/header"
```

**Solution:**
- Confirm the partial file exists in the specified path.
- Check the relative path used in the `include` statement.
- Ensure the partial file has the correct file extension (`.ejs`).

### 5. **Unexpected HTML Output**

**Issue:**
HTML tags are escaped or not rendered as expected.

**Solution:**
- Use `<%- %>` to render unescaped content if needed.
- Ensure you're using the correct EJS tags for your intended output.

---

## Conclusion

EJS is a versatile and straightforward templating engine that enhances your Node.js and Express.js applications by enabling dynamic HTML generation. Its seamless integration, combined with JavaScript's flexibility, makes it an excellent choice for server-side rendering. By understanding its syntax, features, and best practices, you can build robust and maintainable web applications.

**Next Steps:**

- Explore more advanced EJS features and integrate them into your projects.
- Combine EJS with other tools and libraries to enhance functionality.
- Consider learning other templating engines like Pug or Handlebars to compare and choose the best fit for your projects.

Happy coding!