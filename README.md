# Spring-Boot-REST-API

## Tools and Technologies Used

-Java 21

-Spring Boot 3

-Spring Data JPA (Hibernate 6)

-MySQL Database and Workbench

-Intellij IDEA Ultimate

-Maven

-Postman Client

## Development Steps

[1. Spring Boot Application Three-Layer Architecture](#Spring-Boot-Application-Three-Layer-Architecture)

[2. Create and Setup Spring Boot Project in Intellij IDEA](#Setup-Spring-Boot-Project-in-Intellij-IDEA)

[3. Configure MySQL Database in Spring Boot Application](#Configure-MySQL-Database-in-Spring-Boot-Application)

[4. Create Employee JPA Entity](#Create-Employee-JPA-Entity)

[5. Create EmployeeRepository](#Create-EmployeeRepository)

[6. Create EmployeeDto and EmployeeMapper](#Create-EmployeeDto-and-EmployeeMapper)

[7. Creating REST API Operations](#Creating-REST-API-Operations)

[8. Build Add Employee REST API](#Build-Add-Employee-REST-API)

[9. Build Get Employee REST API](#Build-Get-Employee-REST-API)

[10. Build Get All Employees REST API](#Build-Get-All-Employees-REST-API)

[11. Build Update Employeee REST API](#Build-Update-Employeee-REST-API)

[12. Build Delete Employee REST API](#Build-Delete-Employee-REST-API)

[13.How to use Postman in order to test the API](#How-to-use-Postman)

[14.Directory Tree](#Directory-Tree)

## Spring Boot Application Three-Layer Architecture

![Alt Text](https://github.com/Razvan03/Spring-Boot-REST-API/blob/main/SpringBoot%20App/ThreeLayers.png)

This architecture delineates the structure of a Spring Boot application into three primary layers: Presentation, Service, and DAO. External requests from tools like Postman interact with the Presentation layer, which further communicates with the Service layer, eventually reaching the DAO layer for database operations. The layers exchange data using Data Transfer Objects (DTOs) to ensure a clean separation of concerns.

## Layers Explanation:

### 1. Presentation Layer(Controller Layer):

#### Purpose: To receive and respond to external requests, typically HTTP requests, acting as the initial entry point for the application.

#### Contents:

-Controllers: They handle incoming requests, validate input, and return appropriate responses, often in the form of JSON or XML.

-DTOs (Data Transfer Objects): These are simple objects that carry data between processes. In this context, DTOs are used to transfer data between the Presentation layer and the Service layer, abstracting the internal domain model.

### 2. Service Layer:

#### Purpose: To encapsulate the business logic of the application. This layer communicates between the Presentation and DAO layers, ensuring that data is processed, validated, or transformed as needed.

#### Contents:

-Services: They contain core business logic and rules of the application. Any operation that needs to be executed, like calculations, validations, or in my case , CRUD Operations Usage implementations, happens here.

-DTOs: While also present in the Presentation layer, DTOs are used in the Service layer to pass data to and from the DAO layer, ensuring that there's a clear boundary and separation of concerns.

### 3. DAO Layer(Repository Layer):

#### Purpose: Direct interaction with the database. This layer is responsible for creating, reading, updating, and deleting records in the database.

#### Contents:

-Repositories (DAOs): These are interfaces or abstract classes, typically using frameworks like JPA (Java Persistence API). They provide methods to perform CRUD operations and interact directly with the database.

-Entities: Represented as classes, these are the domain models mapped directly to the database tables. They can have annotations to describe relationships, table names, and other database-related configurations.

## Flow:

A user, via Postman, sends a request that first hits the Controller in the Presentation layer. The Controller may use a DTO to send the data to the appropriate Service. The Service processes the data (applying business logic) and interacts with the Repository in the DAO layer, using another DTO if needed. The DAO layer, through the Repository, communicates directly with the database. The result then propagates back up through the layers, and a response is returned to Postman.


## Setup Spring Boot Project in Intellij IDEA

Navigate to [Spring Initializr](https://start.spring.io/) and fill the properties as in the image below:

![Alt Text](https://github.com/Razvan03/Spring-Boot-REST-API/blob/main/SpringBoot%20App/Spring.png)

After this, click on generate and open the downloaded folder in Intellij IDEA.

## Configure MySQL Database in Spring Boot Application

In Intellij IDEA project explorer, navigate to ems-backend/src/main/resources and open application.properties.

Write this:

```text
spring.datasource.url=jdbc:mysql://localhost:3306/ems
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
```

#### spring.datasource.url=jdbc:mysql://localhost:3306/ems

This sets the URL for your MySQL database. It indicates that the database is running on your local machine (localhost) at port 3306 and the name of the database is ems.

#### spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

Hibernate needs to know the SQL dialect to generate appropriate SQL queries for the underlying database. This setting tells Hibernate to use the MySQL dialect, so it can produce MySQL-compatible queries.

#### spring.jpa.hibernate.ddl-auto=update

This is a Hibernate feature which automatically validates or exports database schema changes depending on the value provided. In this case, "update" means Hibernate will update the database schema if necessary (e.g., new entities or changes in existing entities).

## Create Employee JPA Entity

In ems-backend/src/main/java/net.javaproject.ems create a new package named entity and a class named Employee with the following code:

```java
package net.javaproject.ems.entity;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) //auto incremate the primary key
    private Long id;

    @Column(name = "first_name")
    private String firstName;

    @Column(name = "last_name")
    private String lastName;

    @Column(name = "email_id", nullable = false, unique = true)
    private String email;
}
```

The Employee class represents an entity mapped to the "employees" table in the database. It defines attributes like ID, first name, last name, and email. This class is used to interact with employee data in the database using JPA, and Lombok annotations auto-generate its boilerplate code like getters and setters.

## Create EmployeeRepository

In ems-backend/src/main/java/net.javaproject.ems create a new package named repository and an interface named EmployeeRepository that extends JpaRepository.

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```

The EmployeeRepository interface extends JpaRepository, targeting the Employee entity with a primary key of type Long. It provides CRUD operations for the Employee entity without requiring explicit method definitions. In a Spring-based application, it facilitates data access to the "employees" table in the database.

## Create EmployeeDto and EmployeeMapper

Create 2 new packages named dto and mapper, with 2 new java classes, named EmployeeDto, respectively EmployeeMapper.

EmployeeDto:
```java
package net.javaproject.ems.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class EmployeeDto {
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
}
```
The EmployeeDto class is a Data Transfer Object (DTO) representing employee data. It contains attributes like ID, first name, last name, and email. DTOs like this are typically used to transfer data between different layers of an application, abstracting the internal domain model and optimizing the data payload for specific operations or API responses.

EmployeeMapper:
```java
package net.javaproject.ems.mapper;

import net.javaproject.ems.dto.EmployeeDto;
import net.javaproject.ems.entity.Employee;

public class EmployeeMapper {

    public static EmployeeDto mapToEmployeeDto(Employee employee){
        return new EmployeeDto(
          employee.getId(),
          employee.getFirstName(),
          employee.getLastName(),
          employee.getEmail()
        );
    }

    public static Employee mapToEmployee(EmployeeDto employeeDto){
        return new Employee(
                employeeDto.getId(),
                employeeDto.getFirstName(),
                employeeDto.getLastName(),
                employeeDto.getEmail()
        );
    }
}

```

The EmployeeMapper class provides utility methods to convert between the Employee entity and its corresponding EmployeeDto Data Transfer Object. It's used to facilitate the transition of data between different layers of the application, ensuring proper conversion and encapsulation between the domain model and the data representation used for operations or API interactions.

## Creating REST API Operations

### Creating Service package

The EmployeeService interface defines the methods needed for our business operations. EmployeeServiceImpl provides the concrete implementation of these methods, involving interactions with the database through repositories.

Create a new package named Service and an Interface named EmployeeService in which abstract methods are created.

In the Service package create another package named impl and a class named EmployeeServiceImpl, where the implementation of createEmployee will be overridden.

### Creating Controller package

In order to use the methods from the service package, you will need to create a controller package with an EmployeeController java class.

EmployeeController will contain endpoints(like POST for adding an employee) mapped to specific URLs. When an HTTP request hits one of these URLs, the corresponding method in the controller is invoked. This method then calls the service layer to process the request and returns a response, typically in JSON format, along with a HTTP status code.

In essence, in a REST API setup, the controller manages the HTTP interactions and delegates the business operations to the service layer.

## Build Add Employee REST API

### In EmployeeService write the add method:
```EmployeeDto createEmployee(EmployeeDto employeeDto); ```

### EmployeeServiceImpl:
```java
import net.javaproject.ems.mapper.EmployeeMapper;
import net.javaproject.ems.repository.EmployeeRepository;
import net.javaproject.ems.service.EmployeeService;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
@AllArgsConstructor
public class EmployeeServiceImpl implements EmployeeService {
    private EmployeeRepository employeeRepository;
    @Override
    public EmployeeDto createEmployee(EmployeeDto employeeDto) {

        Employee employee = EmployeeMapper.mapToEmployee(employeeDto);
        Employee savedEmployee = employeeRepository.save(employee);
        return EmployeeMapper.mapToEmployeeDto(savedEmployee);
    }
}
```

The createEmployee method in EmployeeServiceImpl takes an EmployeeDto as input, converts it to an Employee entity using the EmployeeMapper, saves the entity to the database through the employeeRepository, and then converts the saved entity back to an EmployeeDto using the mapper, returning the DTO representation of the persisted employee.

### EmployeeController:

```java
@RestController //capable to handle HTTP requests
@RequestMapping("/api/employees") //orice request la /api/employees o sa fie tratat de acest controller (endpoint) si va returna un raspuns
@AllArgsConstructor
public class EmployeeController {
    private EmployeeService employeeService;

    //Build Add Employee REST API
    @PostMapping
    public ResponseEntity<EmployeeDto> createEmployee(@RequestBody EmployeeDto employeeDto){
        //@RequestBody primeste un json data de la user(HTTP Request), il converteste intr-un EmployeeDto
        EmployeeDto savedEmployee = employeeService.createEmployee(employeeDto);
        return new ResponseEntity<>(savedEmployee, HttpStatus.CREATED);
    }
}
```

The createEmployee method in the EmployeeController class handles HTTP POST requests to the /api/employees endpoint.

When a client sends a POST request with a JSON payload to this endpoint:

1.The @RequestBody annotation automatically converts the JSON payload into an EmployeeDto object.

2.This employeeDto object is then passed to the employeeService.createEmployee method, which contains the business logic for saving the employee.

3.Once the employee is successfully saved, the method returns a response with the saved EmployeeDto object and an HTTP status of CREATED (HTTP 201).

## Build Get Employee REST API

### In EmployeeService write the getEmployeeById method:
```EmployeeDto getEmployeeById(Long employeeId);```

### EmployeeServiceImpl:
```java
 @Override
    public EmployeeDto getEmployeeById(Long employeeId) {
        Employee employee = employeeRepository.findById(employeeId)
                .orElseThrow(() ->
                        new ResourceNotFoundException("Employee does not exist with given id : " + employeeId));
        return EmployeeMapper.mapToEmployeeDto(employee);
    }
```

The getEmployeeById method retrieves an employee based on a given employeeId.

Here's how it works:

1. The method calls the ```employeeRepository.findById(employeeId)```, which attempts to fetch an Employee object with the specified employeeId from the database.

2. If the Employee object is found, it gets returned. If not, the orElseThrow method is invoked, which throws a custom exception: ResourceNotFoundException indicating that there's no employee with the provided employeeId.

3. If an Employee object is successfully retrieved, the EmployeeMapper.mapToEmployeeDto method is called to convert the Employee entity into an EmployeeDto Data Transfer Object.

4. Finally, the method returns the EmployeeDto object.

### For the exception create a new package named exception and a class ResourceNotFoundException with the following code:

```java
package net.javaproject.ems.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(value = HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException{
    public ResourceNotFoundException(String message){
        super(message);
    }
}
```

### EmployeeController:
```java
//Build Get Employee REST API
    @GetMapping("{id}")
    public ResponseEntity<EmployeeDto> getEmployeeById(@PathVariable("id") Long employeeId){ //se va pasa parametrul id ca si un PathVariable
        EmployeeDto employeeDto = employeeService.getEmployeeById(employeeId);
        return ResponseEntity.ok(employeeDto);
    }
```

The getEmployeeById method in the controller handles HTTP GET requests to fetch an employee based on a given ID.

Here's how it works:

1. Annotations:

```@GetMapping("{id}")```: This specifies that the method should be invoked for HTTP GET requests where the URL has an additional segment (the {id}), e.g., /api/employees/1.

2. Method Parameters:

```@PathVariable("id") Long employeeId```: This annotation binds the {id} segment from the URL to the employeeId method parameter. For instance, if the request URL is /api/employees/5, employeeId would be 5.

3. Processing:

Inside the method, the ```employeeService.getEmployeeById(employeeId)``` call is made to fetch the employee details corresponding to the given employeeId.

4. Response:

If the EmployeeDto object is successfully retrieved, the method wraps it in a ResponseEntity with an HTTP status of OK (HTTP 200) and returns it. If not, the appropriate exception handling would determine the error response.

## Build Get All Employees REST API

### In EmployeeService write the getAllEmployees method:
``` List<EmployeeDto> getAllEmployees(); ```

### EmployeeServiceImpl:
```java
@Override
    public List<EmployeeDto> getAllEmployees() {
        List<Employee> employees = employeeRepository.findAll();
        //converteste lista de entity in dto
        return employees.stream().map((employee) -> EmployeeMapper.mapToEmployeeDto(employee))
                .collect(Collectors.toList()); //acumulates the elements of the stream into a new list
    }
```

The getAllEmployees method retrieves a list of all employees and converts them from entities to DTOs (Data Transfer Objects).

Here's how it works:

1. Fetching All Employees:

```employeeRepository.findAll()```: This method call fetches all Employee entities from the database and stores them in the employees list.

2. Conversion to DTOs:

The method then utilizes Java streams to process this list.

```employees.stream()```: This initiates a stream of Employee entities.

```.map((employee) -> EmployeeMapper.mapToEmployeeDto(employee))```: For each Employee entity in the stream, the lambda function provided to the map method converts it into an EmployeeDto object using the EmployeeMapper.

```.collect(Collectors.toList())```: This terminal operation accumulates the processed stream elements (now EmployeeDto objects) into a new list.

3. Return:

The method finally returns the list of EmployeeDto objects.

### EmployeeController:

```java
//Build Get All Employees REST API
    @GetMapping
    public ResponseEntity<List<EmployeeDto>> getAllEmployees(){
        List<EmployeeDto> employees = employeeService.getAllEmployees();
        return ResponseEntity.ok(employees);
    }
```

## Build Update Employeee REST API

### In EmployeeService write the updateEmployee method:
``` EmployeeDto updateEmployee(Long employeeId, EmployeeDto updatedEmployee); ```

### EmployeeServiceImpl:
```java
@Override
    public EmployeeDto updateEmployee(Long employeeId, EmployeeDto updatedEmployee) {
        Employee employee = employeeRepository.findById(employeeId).orElseThrow(
                () -> new ResourceNotFoundException("Employee does not exists with given id: " + employeeId)
        );

        employee.setFirstName(updatedEmployee.getFirstName());
        employee.setLastName(updatedEmployee.getLastName());
        employee.setEmail(updatedEmployee.getEmail());

        Employee updatedEmployeeObj = employeeRepository.save(employee);

        return EmployeeMapper.mapToEmployeeDto(updatedEmployeeObj);
    }
```

This updateEmployee method is designed to update an existing employee's details.

Here's how it operates:

1. Find Existing Employee:

-The method first tries to fetch an existing Employee entity with the provided employeeId from the database using ```employeeRepository.findById(employeeId)```.

-If the employee is not found, a ResourceNotFoundException is thrown indicating the absence of the employee with the given ID.

2. Update Employee Details:

-The existing Employee entity's attributes (firstName, lastName, and email) are then updated using values from the updatedEmployee DTO.

3.Persist Changes:

-The modified Employee entity is saved back to the database with ```employeeRepository.save(employee)```, and the updated entity is captured in the updatedEmployeeObj.

4.Convert and Return:

Finally, the updated Employee entity is converted back into an EmployeeDto using the EmployeeMapper and returned.

###EmployeeController:

```java
//Build Update Employee REST API
    @PutMapping("{id}")
    public ResponseEntity<EmployeeDto> updateEmployee(@PathVariable("id") Long employeeId,
                                                      @RequestBody EmployeeDto updatedEmployee){
        EmployeeDto employeeDto = employeeService.updateEmployee(employeeId,updatedEmployee);
        return ResponseEntity.ok(employeeDto);
    }
```

## Build Delete Employee REST API

### In EmployeeService write the deleteEmployee method:
```void deleteEmployee(Long employeeId);```

### EmployeeServiceImpl:
```java
@Override
    public void deleteEmployee(Long employeeId) {
        Employee employee = employeeRepository.findById(employeeId).orElseThrow(
                () -> new ResourceNotFoundException("Employee does not exists with given id: " + employeeId)
        );
        employeeRepository.deleteById(employeeId);
    }
```

The deleteEmployee method is designed to delete an employee based on a given employeeId.

Here's a brief rundown:

1. Retrieve the Employee:
   
The method attempts to find an Employee entity with the provided employeeId from the database using ```employeeRepository.findById(employeeId)```.

2.Exception Handling:

If the specified employee isn't found, the orElseThrow method triggers, throwing a ResourceNotFoundException with a message indicating that no employee exists with the given ID.

3.Delete the Employee:
If the employee exists, the method proceeds to delete the employee from the database using ```employeeRepository.deleteById(employeeId)```.

### EmployeeController:
```java
@DeleteMapping("{id}")
    //Build Delete Employee REST API
    public ResponseEntity<String> deleteEmployee(@PathVariable("id") Long employeeId){
        employeeService.deleteEmployee(employeeId);
        return ResponseEntity.ok("Employee deleted successfully!");
    }
}
```

## How-to-use-Postman:

![Alt Text](https://github.com/Razvan03/Spring-Boot-REST-API/blob/main/SpringBoot%20App/Postman.png)

### For createEmployee method:

In order to test our API, you need to create a new POST request through Postman , write the URL to which the controller method responds ```(http://localhost:8080/api/employees)```, select "body" , then "raw" , then JSON and write the following JSON:
```
{
    "firstName": "name",
    "lastName": "lastName",
    "email":"email@gmail.com"
}
```
You should get the JSON back as a response if the method is correct.

### For getEmployeeById method:
Create an GET request and write the localhost URL followed by /id like this: ```http://localhost:8080/api/employees/1```
You should get back the JSON whose ID is the one mentioned above.

### For getAllEmployees method:
Just create a GET request with the localhost URL:``` http://localhost:8080/api/employees```
You should get a different JSON for every employee in the database.

### For updateEmployee method:
Create a PUT request with the URL followed by the /id of whom you want to update:``` http://localhost:8080/api/employees/1```, select "body" , then "raw" , then JSON and write the following JSON:
following JSON:
```
{
    "firstName": "name",
    "lastName": "lastName",
    "email":"email@gmail.com"
}
```
You should get the JSON back with the attributes updated.

### For deleteEmployee method:

Create a DELETE request with the localhost URL followed by /id of whom you want to delete.
You should get back the message : "Employee deleted successfully!".

## Directory Tree
```
ems-backend
    │   .gitignore
    │   HELP.md
    │   mvnw
    │   mvnw.cmd
    │   pom.xml
    │
    ├───.idea
    │       .gitignore
    │       compiler.xml
    │       encodings.xml
    │       jarRepositories.xml
    │       misc.xml
    │       workspace.xml
    │
    ├───.mvn
    │   └───wrapper
    │           maven-wrapper.jar
    │           maven-wrapper.properties
    │
    ├───src
    │   ├───main
    │   │   ├───java
    │   │   │   └───net
    │   │   │       └───javaproject
    │   │   │           └───ems
    │   │   │               │   EmsBackendApplication.java
    │   │   │               │
    │   │   │               ├───controller
    │   │   │               │       EmployeeController.java
    │   │   │               │
    │   │   │               ├───dto
    │   │   │               │       EmployeeDto.java
    │   │   │               │
    │   │   │               ├───entity
    │   │   │               │       Employee.java
    │   │   │               │
    │   │   │               ├───exception
    │   │   │               │       ResourceNotFoundException.java
    │   │   │               │
    │   │   │               ├───mapper
    │   │   │               │       EmployeeMapper.java
    │   │   │               │
    │   │   │               ├───repository
    │   │   │               │       EmployeeRepository.java
    │   │   │               │
    │   │   │               └───service
    │   │   │                   │   EmployeeService.java
    │   │   │                   │
    │   │   │                   └───impl
    │   │   │                           EmployeeServiceImpl.java
    │   │   │
    │   │   └───resources
    │   │       │   application.properties
    │   │       │
    │   │       ├───static
    │   │       └───templates
    │   └───test
    │       └───java
    │           └───net
    │               └───javaproject
    │                   └───ems
    │                           EmsBackendApplicationTests.java
    │
    └───target
        ├───classes
        │   │   application.properties
        │   │
        │   └───net
        │       └───javaproject
        │           └───ems
        │               │   EmsBackendApplication.class
        │               │
        │               ├───controller
        │               │       EmployeeController.class
        │               │
        │               ├───dto
        │               │       EmployeeDto.class
        │               │
        │               ├───entity
        │               │       Employee.class
        │               │
        │               ├───exception
        │               │       ResourceNotFoundException.class
        │               │
        │               ├───mapper
        │               │       EmployeeMapper.class
        │               │
        │               ├───repository
        │               │       EmployeeRepository.class
        │               │
        │               └───service
        │                   │   EmployeeService.class
        │                   │
        │                   └───impl
        │                           EmployeeServiceImpl.class
        │
        └───generated-sources
            └───annotations
```
