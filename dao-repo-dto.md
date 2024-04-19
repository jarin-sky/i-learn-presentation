# DAO, Repository, DTO


- #### DAO (Data Access Object) 
- #### Repository(eg. JpaRepository)  
- #### Differences between DAO and Repository
- #### DTO (Data Transfer Object) 

## DAO

- #### What is DAO?
Within Spring Boot, the DAO (Data Access Object) pattern is typically used to separate the data access logic from the rest of the application and to ensure proper integration with the data source, such as a database.

DAO stands for Data Access Object. It is a design pattern used in software engineering that separates the business logic of an application from the method of accessing data. DAO provides an abstraction for accessing data in a database or other data storage. This means that DAO provides a unified interface for communicating with the data storage, whether it's a relational database, object-relational mapping (ORM), or other data repository.
- #### Components
  - ##### DAO Interface
  This is the core component of the pattern. It defines a set of methods for performing CRUD (Create, Read, Update, Delete) operations on the data source. It typically includes methods for saving, retrieving, updating, and deleting data objects. The DAO interface serves as a contract that concrete implementations must adhere to.
  
  - ##### DAO Implementation
  Concrete classes that implement the DAO interface. These classes contain the actual logic for interacting with the underlying data source, such as executing SQL queries, calling web services, or accessing a file system. The DAO implementation is responsible for handling the low-level details of data access, such as opening connections, executing queries, and managing transactions.
Overall, repositories in Spring Data provide a convenient and efficient way to interact with data sources, promoting cleaner code organization and improved maintainability of Spring-based applications.
- #### Responsibilities
  - ##### Exception handling
  DAO implementations often handle exceptions that may occur during data access operations, such as database connection failures, query errors, or concurrency issues. Proper exception handling ensures that the application can gracefully recover from errors and provide meaningful feedback to the user.

  - ##### Dependency Injection
  In modern application development frameworks like Spring, DAO instances are often managed as Spring beans and injected into other components using dependency injection. This allows for easier management of dependencies and promotes loose coupling between components.

- #### Using in Spring Boot

```java

@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private double price;
    private int quantity;

    // getters and setters
}


```

```java

public interface ProductDAO {

    Product create(Product product);
    List<Product> getAllProducts();

}

```

```java

@Repository
public class ProductDAOImpl implements ProductDAO {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public Product createProduct(Product product) {
        String sql = "INSERT INTO products (name, price, quantity) VALUES (?, ?, ?)";
        jdbcTemplate.update(sql, product.getName(), product.getPrice(), product.getQuantity());
        return product;
    }

    // Implementation of other methods for working with products using JdbcTemplate
}

```

```java

@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductDAO productDAO;

    @PostMapping
    public Product createProduct(@RequestBody Product product) {
        return productDAO.createProduct(product);
    }
}

```

## Repository

- #### What is Repository?
Repository is another abstraction for data access that is specific to JPA (Java Persistence API), which is a part of Java EE and the Spring Framework. Repository within Spring is specific to Spring Data JPA. In this context, Repository provides a simple and intuitive interface for working with the database using JPA. Repository allows performing common data operations such as creating, reading, updating, and deleting (CRUD operations) using methods defined in the interface.

Under the hood, Spring Data repositories leverage Spring's powerful dependency injection mechanism along with various persistence technologies (such as JPA, MongoDB, Cassandra, Redis, etc.) to provide implementations of the defined repository interfaces at runtime. This allows developers to focus more on business logic and less on boilerplate code related to data access.

Overall, the Repository is a specialized form of DAO that focuses on Object-Relational Mapping in the context of modern frameworks like Spring Data. Repository provides a higher level of abstraction and is often more domain-oriented than the generic DAO.
- #### Using of Repository

```java

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    private String email;
    
    // getters and setters
}

```

```java

import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    // Define custom query methods if needed
}

```

```java

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {
    private final UserRepository userRepository;
    
    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id).orElse(null);
    }
    
    // Other methods for CRUD operations
}

```
## Differences between DAO and Repository

#### Level of Abstraction
- **DAO**: DAO provides a low-level abstraction over the data source, often including detailed methods for data access, such as SQL queries.
- **Repository**: Repository provides a higher level of abstraction oriented towards the domain and is specific to the application's domain model. Repositories often offer methods corresponding to CRUD (Create, Read, Update, Delete) operations and can be enriched with specific query methods.
#### Focus
- **DAO**: DAO is a generic design pattern focused on providing a unified interface for accessing various types of data sources (databases, files, web services, etc.).
- **Repository**: Repository is specific to Object-Relational Mapping (ORM) and is typically used in environments where working with relational databases.
#### Design Approach
- **DAO**: DAO is based on the traditional design pattern that provides a unified interface for data access and separates business logic from data access implementation details.
- **Repository**: Repository is often associated with modern ORM frameworks like Spring Data, which provides generic repositories and supports declarative query definitions.
#### Usage
- **DAO**: DAO can be used for directly executing queries and manipulating data regardless of the domain model.
- **Repository**: Repository is usually designed to be closely tied to the domain model and provides methods corresponding to operations performed on this model.


## DTO (Data Transfer Object)

- #### What is DTO?

DTO in Spring Boot stands for "Data Transfer Object" and is a design pattern commonly used in Spring Boot-based applications.

DTO is used to transfer data between different layers of an application, such as the controller layer and the service layer. By using DTOs, you can separate domain objects (objects representing the core concepts of your application) from objects intended for transferring data across different parts of the application.

For example, if you have an object representing an entity in the database, such as a user (User), you can create a DTO (such as UserDTO) that contains only the information needed for a particular request or response in your application. This allows better control over which data is transferred between different parts of the application and also facilitates easier modifications if the structure of domain objects changes.

Overall, using DTOs in Spring Boot applications helps improve modularity, readability, and maintainability of the code.

- #### Using of DTO

Let's assume we have an entity `User` representing a user of the application:

```java
public class User {
    private Long id;
    private String username;
    private String email;
    // Other attributes, constructors, getters, and setters
}
```

Now, let's create a DTO for users (`UserDTO`), which will contain only certain attributes:

```java
public class UserDTO {
    private String username;
    private String email;
    // Constructors, getters, and setters
}
```

Then, we can create a service for manipulating users and use the DTO for data transfer between the controller and the service:

```java
@Service
public class UserService {

    public UserDTO getUserById(Long id) {
        // Get the user from the database
        User user = userRepository.findById(id).orElseThrow(() -> new UserNotFoundException("User not found"));
        
        // Create DTO
        UserDTO userDTO = new UserDTO();
        userDTO.setUsername(user.getUsername());
        userDTO.setEmail(user.getEmail());
        
        return userDTO;
    }

    // Other methods for working with users
}
```

Finally, we can create a controller that will handle HTTP requests and utilize the DTO for data transfer:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getUserById(@PathVariable Long id) {
        UserDTO userDTO = userService.getUserById(id);
        return ResponseEntity.ok(userDTO);
    }

    // Other methods for manipulating users
}
```

In this example, the DTO `UserDTO` is used for data transfer between the service `UserService` and the controller `UserController`. This allows separation of domain objects (`User`) from objects intended for data transfer across different parts of the application.




