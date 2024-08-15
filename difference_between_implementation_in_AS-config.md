The difference between `implementation(dependency)` and `implementation 'dependency'` lies in the syntax and how the dependency is defined in a build configuration, typically in Gradle for Android development.

1. **`implementation(dependency)`**:
   - This syntax uses a method call to include a dependency. The `dependency` is usually an object or a variable that contains the dependency information.
   - Example:
     ```groovy
     def retrofit_version = '2.9.0'
     implementation("com.squareup.retrofit2:retrofit:$retrofit_version")
     ```
   - In this example, `implementation` is called with a string formed by concatenating the base dependency string with a version variable.

2. **`implementation 'dependency'`**:
   - This syntax is a shorthand version where the dependency is provided directly as a string literal.
   - Example:
     ```groovy
     implementation 'com.squareup.retrofit2:retrofit:2.9.0'
     ```
   - Here, the dependency is specified directly in the string without using variables or other objects.

### Summary
- **`implementation(dependency)`**: Uses a method call and can include variables or expressions to define the dependency.
- **`implementation 'dependency'`**: Uses a direct string literal to define the dependency.

Both approaches will ultimately achieve the same result of including the specified dependency in your project, but `implementation(dependency)` is more flexible when you need to use variables or expressions to define the dependency.
