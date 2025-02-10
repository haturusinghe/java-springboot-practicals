Here's the detailed learning path for your Spring Boot project:

# Session 1: Introduction to Spring Boot & REST APIs


## Step-by-Step Coding
1. Create project via https://start.spring.io
   - Dependencies: Spring Web
2. Create `HelloController.java`:
```java
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello, Spring Boot!";
    }
}
```
3. Create `StudentController.java` skeleton:
```java
@RestController
@RequestMapping("/api/students")
public class StudentController {
    @GetMapping
    public List<Student> getAll() {
        return List.of(); // Empty for now
    }
}
```

## Eclipse Setup
1. Install Eclipse Enterprise Edition
2. Import Maven project: File > Import > Existing Maven Project
3. Right-click project > Maven > Update Project
4. Run as Spring Boot App

## Key Concepts
- `@RestController`: Combines @Controller and @ResponseBody
- MVC: Model (data), View (presentation), Controller (input handling)
- Dependency Injection: Spring manages object creation

## Homework
1. Create a `/status` endpoint showing application status
2. Research difference between @Controller and @RestController
3. Experiment with different HTTP methods (POST, PUT)

## Next Session Prep
- Add these dependencies in `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```


# Session 2: Data Persistence with Spring Data JPA


## Step-by-Step Coding
1. Create `Student.java` entity:
```java
@Entity
public class Student {
    @Id @GeneratedValue
    private Long id;
    private String name;
    private String email;
    // Getters and setters
}
```
2. Create `StudentRepository.java`:
```java
public interface StudentRepository extends JpaRepository<Student, Long> {}
```

## Eclipse Setup
1. Install JPA Tools from Eclipse Marketplace
2. Open H2 console: http://localhost:8080/h2-console
   - JDBC URL: jdbc:h2:mem:testdb

## Key Concepts
- ORM: Object-Relational Mapping
- JPA: Java Persistence API specification
- H2: In-memory database

## Homework
1. Add `createdDate` field with `@CreationTimestamp`
2. Implement soft delete using `@SQLDelete`
3. Write a @DataJpaTest for repository

## Next Session Prep
- Create `service` package
- Study @Service annotation



# Session 3: Service Layer & Dependency Injection


## Step-by-Step Coding
1. Create `StudentService.java`:
```java
@Service
@Transactional
public class StudentService {
    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }
    
    public Student createStudent(Student student) {
        return repository.save(student);
    }
    
    public List<Student> getAllStudents() {
        return repository.findAll();
    }
}
```

2. Update `StudentController.java`:
```java
@RestController
@RequestMapping("/api/students")
public class StudentController {
    private final StudentService service;
    
    public StudentController(StudentService service) {
        this.service = service;
    }
    
    @PostMapping
    public ResponseEntity<Student> createStudent(@RequestBody Student student) {
        return ResponseEntity.ok(service.createStudent(student));
    }
}
```

## Eclipse Setup
1. Install Spring Tools from Eclipse Marketplace
2. Use "Open Type" (Ctrl+Shift+T) to navigate between layers
3. Use "Spring Beans" view to see dependency graph

## Key Concepts
- Service Layer: Business logic separation
- Constructor Injection: Preferred over field injection
- @Transactional: Automatic transaction management
- DTO Pattern: Data transfer objects for API contracts

## Homework
1. Create a StudentDTO with validation annotations
2. Add updateStudent and deleteStudent methods
3. Implement a StudentNotFoundException

## Next Session Prep
Add validation dependency to `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

# Session 4: Validation & Error Handling


## Step-by-Step Coding
1. Add validation to StudentDTO:
```java
public record StudentDTO(
    @NotBlank String name,
    @Email String email
) {}
```

2. Create `GlobalExceptionHandler.java`:
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(MethodArgumentNotValidException ex) {
        // Create error response from binding results
    }
}
```

## Eclipse Setup
1. Enable annotation processing: Project > Properties > Java Compiler > Annotation Processing
2. Use "Quick Fix" (Ctrl+1) to add missing exception handlers

## Key Concepts
- Bean Validation: JSR-380 standard annotations
- @ControllerAdvice: Global exception handling
- Error Response Standardization: Consistent API error format
- HTTP Status Codes: 400 vs 500 errors

## Homework
1. Add custom @AdultAge validation for birthdate
2. Implement rate limiting error handling
3. Create specific error types for duplicate emails

## Next Session Prep
Add security dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

# Session 5: Security with Spring Security


## Step-by-Step Coding
1. Create `SecurityConfig.java`:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/api/students/**").hasRole("ADMIN")
            .and()
            .httpBasic();
        return http.build();
    }
}
```

2. Add in-memory user:
```properties
spring.security.user.name=admin
spring.security.user.password=secret
spring.security.user.roles=ADMIN
```

## Eclipse Setup
1. Install "Spring Security" tools from Eclipse Marketplace
2. Use "Properties" view to edit application.properties
3. Use "Outline" view to navigate security config

## Key Concepts
- Authentication vs Authorization
- Password Encoders: BCrypt best practices
- CSRF: State-changing request protection
- Role Hierarchy: ADMIN > USER

## Homework
1. Implement BCrypt password encoding
2. Add USER role with limited access
3. Create login endpoint with JWT (bonus)

## Next Session Prep
Add test dependencies:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
</dependency>
```

# Session 6: Testing & Deployment


## Step-by-Step Coding
1. Create `StudentServiceTest.java`:
```java
@ExtendWith(MockitoExtension.class)
class StudentServiceTest {
    @Mock StudentRepository repository;
    @InjectMocks StudentService service;
    
    @Test
    void shouldCreateStudent() {
        // Mock repository and test service
    }
}
```

2. Add `application-prod.properties`:
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/studentdb
spring.jpa.hibernate.ddl-auto=validate
```

## Eclipse Setup
1. Install TestNG from Eclipse Marketplace
2. Use "Coverage" view for test coverage
3. Configure Run Configurations for different profiles

## Key Concepts
- Test Pyramid: Unit > Integration > E2E
- Testcontainers: Real database testing
- Profile-based Configuration: Environment-specific settings
- JAR Packaging: Executable deployment format

## Homework
1. Write integration test for StudentController
2. Configure PostgreSQL Testcontainer
3. Create Dockerfile for application

## Next Session Prep
- Study Spring Boot Actuator for monitoring
- Explore deployment options (Heroku, AWS Elastic Beanstalk)
```

This complete learning path takes students from basic REST APIs to a secured, tested, production-ready application. Each session builds on previous work while introducing professional development practices. The Eclipse-specific guidance helps overcome common IDE hurdles, while the homework assignments encourage deeper exploration of each topic.