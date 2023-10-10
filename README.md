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

4. Create Employee JPA Entity

5. Create EmployeeRepository

6. Create EmployeeDto and EmployeeMapper

7. Build Add Employee REST API

8. Build Get Employee REST API

9. Build Get All Employees REST API

10. Build Update Employeee REST API

11. Build Delete Employee REST API

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

In Intellij IDEA project explorer, navigate to projectName/src/main/resources and open application.properties.

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
