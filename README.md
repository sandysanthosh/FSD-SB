Creating a full-stack application involves both front-end and back-end development. We'll use Spring Boot for the back end and Bootstrap along with HTML/CSS/JavaScript for the front end. Here's a step-by-step guide to creating a simple full-stack application.

### Back-End (Spring Boot)

1. **Set Up Your Spring Boot Project:**

   Use Spring Initializr or your preferred IDE to create a new Spring Boot project with the following dependencies:
   - Spring Web
   - Spring Data JPA
   - H2 Database (or MySQL for a more production-like setup)

2. **Configure Your Application:**

   In `application.properties`, configure your database settings. For H2, it might look like this:

   ```properties
   spring.datasource.url=jdbc:h2:mem:testdb
   spring.datasource.driverClassName=org.h2.Driver
   spring.datasource.username=sa
   spring.datasource.password=password
   spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
   ```

3. **Create Your JPA Entity:**

   ```java
   package com.example.demo.model;

   import javax.persistence.Entity;
   import javax.persistence.GeneratedValue;
   import javax.persistence.GenerationType;
   import javax.persistence.Id;

   @Entity
   public class User {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       private String name;
       private String email;

       // Getters and Setters
   }
   ```

4. **Create Your Repository:**

   ```java
   package com.example.demo.repository;

   import com.example.demo.model.User;
   import org.springframework.data.jpa.repository.JpaRepository;

   public interface UserRepository extends JpaRepository<User, Long> {
   }
   ```

5. **Create Your Service:**

   ```java
   package com.example.demo.service;

   import com.example.demo.model.User;
   import com.example.demo.repository.UserRepository;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;

   import java.util.List;

   @Service
   public class UserService {
       @Autowired
       private UserRepository userRepository;

       public List<User> getAllUsers() {
           return userRepository.findAll();
       }

       public User saveUser(User user) {
           return userRepository.save(user);
       }

       public void deleteUser(Long id) {
           userRepository.deleteById(id);
       }
   }
   ```

6. **Create Your Controller:**

   ```java
   package com.example.demo.controller;

   import com.example.demo.model.User;
   import com.example.demo.service.UserService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.web.bind.annotation.*;

   import java.util.List;

   @RestController
   @RequestMapping("/api/users")
   public class UserController {
       @Autowired
       private UserService userService;

       @GetMapping
       public List<User> getAllUsers() {
           return userService.getAllUsers();
       }

       @PostMapping
       public User createUser(@RequestBody User user) {
           return userService.saveUser(user);
       }

       @DeleteMapping("/{id}")
       public void deleteUser(@PathVariable Long id) {
           userService.deleteUser(id);
       }
   }
   ```

### Front-End (HTML, CSS, JavaScript, Bootstrap)

1. **Create a Basic HTML Structure:**

   In `src/main/resources/static`, create an `index.html` file:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Full Stack Application</title>
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
       <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
   </head>
   <body>
       <div class="container">
           <h1 class="my-4">User Management</h1>
           <div class="mb-3">
               <input type="text" id="name" class="form-control" placeholder="Name">
               <input type="email" id="email" class="form-control" placeholder="Email">
               <button class="btn btn-primary mt-2" onclick="createUser()">Add User</button>
           </div>
           <ul id="userList" class="list-group"></ul>
       </div>
       <script>
           $(document).ready(function() {
               fetchUsers();
           });

           function fetchUsers() {
               $.ajax({
                   url: "/api/users",
                   method: "GET",
                   success: function(data) {
                       var userList = $("#userList");
                       userList.empty();
                       data.forEach(function(user) {
                           userList.append('<li class="list-group-item">' + user.name + ' (' + user.email + ') <button class="btn btn-danger btn-sm float-right" onclick="deleteUser(' + user.id + ')">Delete</button></li>');
                       });
                   }
               });
           }

           function createUser() {
               var name = $("#name").val();
               var email = $("#email").val();
               $.ajax({
                   url: "/api/users",
                   method: "POST",
                   contentType: "application/json",
                   data: JSON.stringify({ name: name, email: email }),
                   success: function() {
                       $("#name").val("");
                       $("#email").val("");
                       fetchUsers();
                   }
               });
           }

           function deleteUser(id) {
               $.ajax({
                   url: "/api/users/" + id,
                   method: "DELETE",
                   success: function() {
                       fetchUsers();
                   }
               });
           }
       </script>
   </body>
   </html>
   ```

### Running the Application

1. **Run the Spring Boot Application:**

   Make sure your main application class is set up correctly:

   ```java
   package com.example.demo;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class DemoApplication {
       public static void main(String[] args) {
           SpringApplication.run(DemoApplication.class, args);
       }
   }
   ```

2. **Access the Application:**

   Open your browser and navigate to `http://localhost:8080`. You should see a simple interface to add, list, and delete users.

This setup demonstrates a basic full-stack application using Spring Boot for the back end and HTML/Bootstrap for the front end, with jQuery for AJAX requests. You can expand this by adding more features, using frameworks like React or Angular for the front end, and a more robust database for production use.
