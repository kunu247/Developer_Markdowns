Creating a simple web application using Java, MS SQL Server, Apache Tomcat, Maven, and JSP technology involves several steps. Below, I will outline the complete process, including the project structure, necessary code, configuration files, and dependencies.

### Project Overview

**Project Name**: MobileShopCRUDApp  
**Functionality**: Full CRUD operations for managing products in an Android and iOS mobile shop.  
**Technologies**: Java, JSP, Spring MVC, MS SQL Server, Apache Tomcat, Maven.

### 1. Project Setup

#### Step 1: Install Required Tools
- **NetBeans IDE**: Ensure you have the latest version installed.
- **JDK**: Install JDK 17 or later.
- **Apache Tomcat**: Download and install Apache Tomcat (preferably version 10 for Jakarta EE).
- **MS SQL Server**: Ensure you have a working installation of MS SQL Server.

#### Step 2: Create a New Maven Project in NetBeans
1. Open NetBeans and select **File → New Project**.
2. Choose **Maven** from the project categories, then select **Web Application**.
3. Name your project `MobileShopCRUDApp`, set the base package to `com.mobileshop`, and select the latest version of the Java Platform.
4. Click **Finish** to create the project.

#### Step 3: Add Dependencies to `pom.xml`
Open the `pom.xml` file and add the necessary dependencies for Spring MVC, Jakarta EE, Tomcat, MS SQL Server, and JSP support.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mobileshop</groupId>
    <artifactId>MobileShopCRUDApp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <java.version>17</java.version>
        <spring.version>5.3.10</spring.version>
        <hibernate.version>5.6.5.Final</hibernate.version>
        <sqljdbc.version>9.4.1.jre17</sqljdbc.version>
    </properties>

    <dependencies>
        <!-- Spring Web MVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Spring ORM -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Hibernate -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>

        <!-- MS SQL Server Driver -->
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>${sqljdbc.version}</version>
        </dependency>

        <!-- JSP API -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>

        <!-- JSTL API -->
        <dependency>
            <groupId>javax.servlet.jsp.jstl</groupId>
            <artifactId>javax.servlet.jsp.jstl-api</artifactId>
            <version>1.2.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.1</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### 2. File Structure

Your project structure will look like this:

```plaintext
MobileShopCRUDApp/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/mobileshop/
│   │   │       ├── controller/
│   │   │       │   └── ProductController.java
│   │   │       ├── model/
│   │   │       │   └── Product.java
│   │   │       ├── repository/
│   │   │       │   └── ProductRepository.java
│   │   │       └── service/
│   │   │           └── ProductService.java
│   │   └── resources/
│   │       └── application.properties
│   └── webapp/
│       └── WEB-INF/
│           ├── web.xml
│           └── views/
│               ├── list.jsp
│               ├── create.jsp
│               ├── update.jsp
│               └── delete.jsp
├── pom.xml
└── README.md
```

### 3. Application Files

#### **Step 1: `Product` Entity Class**
Create a `Product.java` model in the `com.mobileshop.model` package.

```java
package com.mobileshop.model;

import javax.persistence.*;

@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String brand;
    private String os; // Android/iOS
    private Double price;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public String getOs() {
        return os;
    }

    public void setOs(String os) {
        this.os = os;
    }

    public Double getPrice() {
        return price;
    }

    public void setPrice(Double price) {
        this.price = price;
    }
}
```

#### **Step 2: `ProductRepository` Interface**
Create a `ProductRepository.java` in the `com.mobileshop.repository` package.

```java
package com.mobileshop.repository;

import com.mobileshop.model.Product;
import org.springframework.stereotype.Repository;
import org.springframework.data.jpa.repository.JpaRepository;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```

#### **Step 3: `ProductService` Class**
Create a `ProductService.java` in the `com.mobileshop.service` package.

```java
package com.mobileshop.service;

import com.mobileshop.model.Product;
import com.mobileshop.repository.ProductRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }

    public Product getProductById(Long id) {
        return productRepository.findById(id).orElse(null);
    }

    public void saveProduct(Product product) {
        productRepository.save(product);
    }

    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }
}
```

#### **Step 4: `ProductController` Class**
Create a `ProductController.java` in the `com.mobileshop.controller` package.

```java
package com.mobileshop.controller;

import com.mobileshop.model.Product;
import com.mobileshop.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@Controller
@RequestMapping("/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/")
    public String viewHomePage(Model model) {
        List<Product> products = productService.getAllProducts();
        model.addAttribute("products", products);
        return "list"; // JSP file name without .jsp extension
    }

    @GetMapping("/create")
    public String showCreateForm(Model model) {
        model.addAttribute("product", new Product());
        return "create";
    }

    @PostMapping("/save")
    public String saveProduct(@ModelAttribute Product product) {
        productService.saveProduct(product);
        return "redirect:/products/";
    }

    @GetMapping("/update/{id}")
    public String showUpdateForm(@PathVariable Long id, Model model) {
        Product product = productService.getProductById(id);
        model.addAttribute("product", product);
       

 return "update";
    }

    @PostMapping("/update")
    public String updateProduct(@ModelAttribute Product product) {
        productService.saveProduct(product);
        return "redirect:/products/";
    }

    @GetMapping("/delete/{id}")
    public String deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return "redirect:/products/";
    }
}
```

#### **Step 5: `web.xml` Configuration**
Create a `web.xml` file in `src/main/webapp/WEB-INF/`.

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/dispatcher-config.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

#### **Step 6: Spring Configuration**
Create a directory `src/main/webapp/WEB-INF/spring/` and add a file `dispatcher-config.xml`.

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.mobileshop" />
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

### 4. JSP Files

#### **Step 1: `list.jsp`**
Create `list.jsp` in `src/main/webapp/WEB-INF/views/`.

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
    <title>Product List</title>
</head>
<body>
<h1>Products</h1>
<a href="${pageContext.request.contextPath}/products/create">Create New Product</a>
<table border="1">
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Brand</th>
        <th>OS</th>
        <th>Price</th>
        <th>Actions</th>
    </tr>
    <c:forEach var="product" items="${products}">
        <tr>
            <td>${product.id}</td>
            <td>${product.name}</td>
            <td>${product.brand}</td>
            <td>${product.os}</td>
            <td>${product.price}</td>
            <td>
                <a href="${pageContext.request.contextPath}/products/update/${product.id}">Edit</a>
                <a href="${pageContext.request.contextPath}/products/delete/${product.id}">Delete</a>
            </td>
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

#### **Step 2: `create.jsp`**
Create `create.jsp` in `src/main/webapp/WEB-INF/views/`.

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <title>Create Product</title>
</head>
<body>
<h1>Create Product</h1>
<form action="${pageContext.request.contextPath}/products/save" method="post">
    <label>Name:</label>
    <input type="text" name="name" required /><br>
    <label>Brand:</label>
    <input type="text" name="brand" required /><br>
    <label>OS:</label>
    <input type="text" name="os" required /><br>
    <label>Price:</label>
    <input type="number" name="price" step="0.01" required /><br>
    <input type="submit" value="Save" />
</form>
</body>
</html>
```

#### **Step 3: `update.jsp`**
Create `update.jsp` in `src/main/webapp/WEB-INF/views/`.

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <title>Update Product</title>
</head>
<body>
<h1>Update Product</h1>
<form action="${pageContext.request.contextPath}/products/update" method="post">
    <input type="hidden" name="id" value="${product.id}" />
    <label>Name:</label>
    <input type="text" name="name" value="${product.name}" required /><br>
    <label>Brand:</label>
    <input type="text" name="brand" value="${product.brand}" required /><br>
    <label>OS:</label>
    <input type="text" name="os" value="${product.os}" required /><br>
    <label>Price:</label>
    <input type="number" name="price" step="0.01" value="${product.price}" required /><br>
    <input type="submit" value="Update" />
</form>
</body>
</html>
```

### 5. Database Setup

1. Create a database named `MobileShop` in your MS SQL Server.
2. Create a table named `products` with the following SQL script:

```sql
CREATE TABLE products (
    id INT PRIMARY KEY IDENTITY(1,1),
    name NVARCHAR(100),
    brand NVARCHAR(100),
    os NVARCHAR(50),
    price DECIMAL(10, 2)
);
```

### 6. Configure Data Source in `application.properties`

Create a file `application.properties` in `src/main/resources/`.

```properties
spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=MobileShop
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### 7. Running the Application

1. **Build the project** using Maven in NetBeans (`Run → Clean and Build Project`).
2. **Deploy the WAR file** generated in the `target` directory to your Apache Tomcat server.
3. Access the application in your web browser at `http://localhost:8080/MobileShopCRUDApp/products/`.

### Summary

You now have a simple CRUD web application set up using Java, Spring MVC, JSP, and MS SQL Server. The project structure is organized, and the necessary files and configurations are provided for a smooth development experience. You can expand this application further by adding more features as needed.