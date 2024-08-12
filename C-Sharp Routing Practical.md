To create routes for CRUD methods and custom methods in an ASP.NET Standard Framework application without Entity Framework, you can follow the step-by-step guide below. This example assumes you are building a simple controller for managing student information, with routes for each CRUD operation, along with a custom method.

### 1. **Setting Up Your ASP.NET MVC Project**

Start by creating an ASP.NET MVC project in Visual Studio:
- Open Visual Studio.
- Select **File > New > Project**.
- Choose **ASP.NET Web Application (.NET Framework)**.
- Select **MVC** and ensure that the **No Authentication** option is selected.

### 2. **Creating the Student Model**

First, create a `StudentModel` class, which will represent the data for each student:

```csharp
using System;

namespace YourNamespace.Models
{
    public class StudentModel
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string UserName { get; set; }
        public string Email { get; set; }
        public int Age { get; set; }
    }
}
```

### Explanation:
- `public int Id { get; set; }`: Defines a property `Id` which uniquely identifies each student.
- `public string Name { get; set; }`: Stores the student's name.
- `public string UserName { get; set; }`: Stores the student's username.
- `public string Email { get; set; }`: Stores the student's email address.
- `public int Age { get; set; }`: Stores the student's age.

### 3. **Creating the StudentController**

Next, create a controller named `StudentController` where you'll define the CRUD methods and routes.

```csharp
using System.Collections.Generic;
using System.Web.Mvc;
using YourNamespace.Models;

namespace YourNamespace.Controllers
{
    public class StudentController : Controller
    {
        private static List<StudentModel> students = new List<StudentModel>();

        // GET: Student/Index
        public ActionResult Index()
        {
            return View(students);
        }

        // GET: Student/Details/5
        public ActionResult Details(int id)
        {
            var student = students.Find(s => s.Id == id);
            if (student == null)
            {
                return HttpNotFound();
            }
            return View(student);
        }

        // GET: Student/Create
        public ActionResult Create()
        {
            return View();
        }

        // POST: Student/Create
        [HttpPost]
        public ActionResult Create(StudentModel student)
        {
            if (ModelState.IsValid)
            {
                student.Id = students.Count + 1;
                students.Add(student);
                return RedirectToAction("Index");
            }
            return View(student);
        }

        // GET: Student/Edit/5
        public ActionResult Edit(int id)
        {
            var student = students.Find(s => s.Id == id);
            if (student == null)
            {
                return HttpNotFound();
            }
            return View(student);
        }

        // POST: Student/Edit/5
        [HttpPost]
        public ActionResult Edit(int id, StudentModel updatedStudent)
        {
            var student = students.Find(s => s.Id == id);
            if (student == null)
            {
                return HttpNotFound();
            }

            if (ModelState.IsValid)
            {
                student.Name = updatedStudent.Name;
                student.UserName = updatedStudent.UserName;
                student.Email = updatedStudent.Email;
                student.Age = updatedStudent.Age;
                return RedirectToAction("Index");
            }
            return View(updatedStudent);
        }

        // GET: Student/Delete/5
        public ActionResult Delete(int id)
        {
            var student = students.Find(s => s.Id == id);
            if (student == null)
            {
                return HttpNotFound();
            }
            return View(student);
        }

        // POST: Student/Delete/5
        [HttpPost, ActionName("Delete")]
        public ActionResult DeleteConfirmed(int id)
        {
            var student = students.Find(s => s.Id == id);
            if (student != null)
            {
                students.Remove(student);
            }
            return RedirectToAction("Index");
        }

        // Custom Method Example: GET: Student/FindByName
        public ActionResult FindByName(string name)
        {
            var student = students.Find(s => s.Name == name);
            if (student == null)
            {
                return HttpNotFound();
            }
            return View(student);
        }
    }
}
```

### Explanation of Key Sections:

1. **List of Students**:
    ```csharp
    private static List<StudentModel> students = new List<StudentModel>();
    ```
   - This static list simulates a database. It will store `StudentModel` objects in memory.

2. **Index Method (Read all students)**:
    ```csharp
    public ActionResult Index()
    {
        return View(students);
    }
    ```
   - Returns a view displaying all students.
   - The `Index` action is typically mapped to the `/Student/Index` route.

3. **Details Method (Read a specific student)**:
    ```csharp
    public ActionResult Details(int id)
    {
        var student = students.Find(s => s.Id == id);
        if (student == null)
        {
            return HttpNotFound();
        }
        return View(student);
    }
    ```
   - This method takes an `id` parameter, finds the corresponding student, and returns the details view.
   - The route `/Student/Details/5` would call this method with `id = 5`.

4. **Create Method (Display form to create a student)**:
    ```csharp
    public ActionResult Create()
    {
        return View();
    }
    ```
   - This method returns a view containing a form to create a new student.

5. **Create Method (Process form submission)**:
    ```csharp
    [HttpPost]
    public ActionResult Create(StudentModel student)
    {
        if (ModelState.IsValid)
        {
            student.Id = students.Count + 1;
            students.Add(student);
            return RedirectToAction("Index");
        }
        return View(student);
    }
    ```
   - This method processes the form submission from the `Create` view.
   - It checks if the model state is valid, assigns a new `Id` to the student, adds them to the list, and redirects to the `Index` view.

6. **Edit Method (Display form to edit a student)**:
    ```csharp
    public ActionResult Edit(int id)
    {
        var student = students.Find(s => s.Id == id);
        if (student == null)
        {
            return HttpNotFound();
        }
        return View(student);
    }
    ```
   - Similar to the `Details` method, but it returns a view containing a form for editing the student's details.

7. **Edit Method (Process edit submission)**:
    ```csharp
    [HttpPost]
    public ActionResult Edit(int id, StudentModel updatedStudent)
    {
        var student = students.Find(s => s.Id == id);
        if (student == null)
        {
            return HttpNotFound();
        }

        if (ModelState.IsValid)
        {
            student.Name = updatedStudent.Name;
            student.UserName = updatedStudent.UserName;
            student.Email = updatedStudent.Email;
            student.Age = updatedStudent.Age;
            return RedirectToAction("Index");
        }
        return View(updatedStudent);
    }
    ```
   - This method updates the student’s details based on the submitted form.

8. **Delete Method (Display form to confirm deletion)**:
    ```csharp
    public ActionResult Delete(int id)
    {
        var student = students.Find(s => s.Id == id);
        if (student == null)
        {
            return HttpNotFound();
        }
        return View(student);
    }
    ```
   - Shows a confirmation view before deleting the student.

9. **Delete Method (Process deletion)**:
    ```csharp
    [HttpPost, ActionName("Delete")]
    public ActionResult DeleteConfirmed(int id)
    {
        var student = students.Find(s => s.Id == id);
        if (student != null)
        {
            students.Remove(student);
        }
        return RedirectToAction("Index");
    }
    ```
   - This method removes the student from the list and redirects to the `Index` view.

10. **Custom Method (Find a student by name)**:
    ```csharp
    public ActionResult FindByName(string name)
    {
        var student = students.Find(s => s.Name == name);
        if (student == null)
        {
            return HttpNotFound();
        }
        return View(student);
    }
    ```
   - This custom method allows you to search for a student by their name.

### 4. **Setting Up Views**

Create the corresponding views under the `Views/Student` folder for each action (`Index`, `Details`, `Create`, `Edit`, `Delete`, `FindByName`).

### 5. **Running the Application**

- Press **F5** or click **Start** to run the application.
- Navigate through the routes like `/Student/Index`, `/Student/Create`, `/Student/Edit/5`, `/Student/Delete/5`, `/Student/FindByName?name=John`.

This setup provides a basic understanding of how to create routes for CRUD operations and custom methods in an ASP.NET Standard MVC application without using Entity Framework.