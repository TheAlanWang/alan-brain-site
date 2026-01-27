## 0) What is Spring Boot?

**Spring Boot** is a framework that makes it fast to build **production-ready Spring applications** by providing:
- **Auto-configuration** (configures things for you based on dependencies)
- **Starters** (dependency bundles like `spring-boot-starter-web`)
- **Embedded server** (Tomcat/Jetty/Undertow) so you can run with `java -jar`
- **Production features** via **Actuator**, metrics, health checks, etc.

**One-liner:** Spring Boot is “Spring + sensible defaults + production tooling”.

---

## 1) Spring vs Spring Boot

- **Spring Framework**: dependency injection (IoC), MVC, AOP, etc.
- **Spring Boot**: opinionated setup + auto-config + starters + production features

You can build web apps with Spring without Boot, but Boot removes boilerplate.

---

## 2) How a Boot app starts

Typical entry point:
```java
@SpringBootApplication
public class App {
  public static void main(String[] args) {
    SpringApplication.run(App.class, args);
  }
}
```

`@SpringBootApplication` = combination of:
- `@Configuration`
- `@EnableAutoConfiguration`
- `@ComponentScan`

**Startup flow (high level)**:
1. Create `ApplicationContext`
2. Scan components (`@Component`, `@Service`, `@Repository`, `@Controller`)
3. Apply auto-configurations
4. Start embedded server (for web apps)
5. App is ready to serve requests

---

## 3) Core: IoC / DI (Dependency Injection)

### Key concept
Spring manages objects as **beans** in an **ApplicationContext**.
You declare dependencies, Spring injects them.

### Common annotations
- `@Component` (generic bean)
- `@Service` (service layer)
- `@Repository` (data access layer)
- `@Controller` / `@RestController` (web layer)
- `@Autowired` (injection — prefer constructor injection)

### Constructor injection (recommended)
```java
@Service
public class UserService {
  private final UserRepository repo;

  public UserService(UserRepository repo) {
    this.repo = repo;
  }
}
```

Why? Easier testing, immutability, clearer dependencies.

---

## 4) Configuration: application.yml / properties

Common files:
- `application.yml` or `application.properties`

Example:
```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/app
    username: app
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
```

### Profiles (dev/test/prod)
- `application-dev.yml`
- `application-prod.yml`

Activate profile:
- env var: `SPRING_PROFILES_ACTIVE=dev`
- or CLI: `--spring.profiles.active=dev`

---

## 5) Auto-configuration & Starters

### Starters
- `spring-boot-starter-web` (Spring MVC + embedded Tomcat + JSON)
- `spring-boot-starter-data-jpa` (JPA + Hibernate)
- `spring-boot-starter-test` (JUnit/Mockito/Spring Test)
- `spring-boot-starter-security` (Spring Security)
- `spring-boot-starter-actuator` (health/metrics endpoints)

### Auto-configuration
Boot looks at your classpath + settings and configures beans automatically.
Example: include `spring-boot-starter-data-jpa` → Boot configures `EntityManagerFactory`, DataSource, etc.

---

## 6) Web: Controllers & REST

### REST controller
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
  private final UserService service;

  public UserController(UserService service) { this.service = service; }

  @GetMapping("/{id}")
  public UserDto getUser(@PathVariable Long id) {
    return service.getUser(id);
  }

  @PostMapping
  public UserDto create(@RequestBody CreateUserRequest req) {
    return service.create(req);
  }
}
```

### Common REST best practices
- `GET /users` list
- `POST /users` create
- `GET /users/{id}` read
- `PATCH /users/{id}` partial update
- `DELETE /users/{id}` delete
- Use correct status codes (`201`, `204`, etc.)

---

## 7) Validation (Bean Validation)

Add dependency: `spring-boot-starter-validation`

DTO validation:
```java
public class CreateUserRequest {
  @NotBlank
  private String name;

  @Email
  private String email;
}
```

Controller:
```java
@PostMapping
public UserDto create(@Valid @RequestBody CreateUserRequest req) {
  return service.create(req);
}
```

---

## 8) Exception Handling (Global)

Use `@ControllerAdvice`:
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

  @ExceptionHandler(EntityNotFoundException.class)
  public ResponseEntity<?> notFound(EntityNotFoundException ex) {
    return ResponseEntity.status(404).body(Map.of("error", ex.getMessage()));
  }

  @ExceptionHandler(MethodArgumentNotValidException.class)
  public ResponseEntity<?> validation(MethodArgumentNotValidException ex) {
    return ResponseEntity.badRequest().body(Map.of("error", "Validation failed"));
  }
}
```

---

## 9) Data Layer: Spring Data JPA

### Entity
```java
@Entity
public class User {
  @Id @GeneratedValue
  private Long id;

  private String name;
  private String email;
}
```

### Repository
```java
public interface UserRepository extends JpaRepository<User, Long> {
  Optional<User> findByEmail(String email);
}
```

### Service layer
```java
@Service
public class UserService {
  private final UserRepository repo;

  public UserService(UserRepository repo) { this.repo = repo; }

  public UserDto getUser(Long id) {
    User u = repo.findById(id).orElseThrow(() -> new EntityNotFoundException("User not found"));
    return toDto(u);
  }
}
```

### Pagination & Sorting
```java
Page<User> page = repo.findAll(PageRequest.of(0, 20, Sort.by("id").descending()));
```

---

## 10) Transactions

Use `@Transactional` for consistency:
```java
@Service
public class PaymentService {
  @Transactional
  public void pay(...) {
    // write operations here
  }
}
```

Key ideas:
- Transactions ensure **atomicity** for a group of DB operations.
- Default rollback: runtime exceptions.
- Don’t put heavy external calls inside long transactions (avoid lock time).

---

## 11) Security (What you should know)

### Spring Security basics
- Authentication (who are you?)
- Authorization (what can you do?)

With starter security, endpoints are secured by default.

### Common patterns
- Session-based auth (traditional web)
- Token auth (JWT/OAuth2) for APIs

If you say “I used Spring Security”, you should know:
- `SecurityFilterChain`
- `AuthenticationManager`
- how to protect endpoints + allow public endpoints
- role/scope checks

Example skeleton:
```java
@Bean
SecurityFilterChain security(HttpSecurity http) throws Exception {
  return http
    .csrf(csrf -> csrf.disable())
    .authorizeHttpRequests(auth -> auth
      .requestMatchers("/health", "/api/auth/**").permitAll()
      .anyRequest().authenticated()
    )
    .build();
}
```

---

## 12) Observability: Actuator

Add: `spring-boot-starter-actuator`

Common endpoints:
- `/actuator/health`
- `/actuator/metrics`
- `/actuator/info`

Expose in config (example):
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
```

---

## 13) Logging

Default logging via **SLF4J** + Logback.

Use:
```java
private static final Logger log = LoggerFactory.getLogger(MyClass.class);
log.info("User created: {}", userId);
```

Tips:
- Log request id / correlation id in distributed systems
- Don’t log secrets (tokens, passwords)

---

## 14) Testing (Very Important)

### What to know
- Unit tests: JUnit + Mockito
- Slice tests: `@WebMvcTest` for controllers
- Integration tests: `@SpringBootTest` + Testcontainers (optional)

Examples:
```java
@WebMvcTest(UserController.class)
class UserControllerTest { ... }
```

```java
@SpringBootTest
class AppIntegrationTest { ... }
```

Common tools:
- `MockMvc` for controller tests
- Mockito for mocking dependencies

---

## 15) Build & Run (Maven)

Common commands:
- `mvn clean test`
- `mvn spring-boot:run`
- `mvn clean package` → outputs a JAR under `target/`

Run:
- `java -jar target/app.jar`

---

## 16) Packaging & Deployment

### Docker (typical)
- Build JAR
- Create Dockerfile
- Deploy to ECS/K8s/VM

### 12-factor mindset (good interview signal)
- Externalize config (env vars)
- Stateless services
- Logs to stdout
- Health checks with actuator

---

## 17) Common Spring Boot Interview Questions (Quick Answers)

1) **What does `@SpringBootApplication` do?**  
   Enables component scan + auto-config + config class.

2) **Difference between `@Controller` and `@RestController`?**  
   `@RestController` = `@Controller` + `@ResponseBody` (returns JSON by default).

3) **Why constructor injection?**  
   Testable, immutable, explicit dependencies.

4) **What is auto-configuration?**  
   Boot configures beans based on classpath and properties.

5) **`PUT` vs `PATCH`?**  
   PUT replaces whole resource; PATCH updates partial fields.

6) **How do you handle global exceptions?**  
   `@ControllerAdvice` / `@RestControllerAdvice`.

7) **What is `@Transactional`?**  
   Wraps DB operations in a transaction; ensures atomicity.

---

## 18) Practical “Default Project Structure” (Recommended)

```
src/main/java/com/example/app
  controller/
  service/
  repository/
  model/ (entities)
  dto/
  config/
src/main/resources
  application.yml
test/
```

---

## 19) Spring Boot Best Practices

- Separate layers (controller/service/repo)
- Validate inputs (`@Valid`)
- Consistent error response model
- Use DTOs (don’t expose entities directly)
- Use pagination for list endpoints
- Keep controllers thin; business logic in services
- Use profiles for dev/test/prod
- Add actuator health checks
- Write tests: unit + integration critical paths

---

## 20) Cheat Sheet
- Boot = auto-config + starters + embedded server + actuator
- `@SpringBootApplication` = config + auto-config + scan
- DI via constructor injection
- REST controllers: `@RestController`, `@RequestMapping`
- Validation: `@Valid` + bean validation annotations
- Exceptions: `@RestControllerAdvice`
- JPA: `@Entity` + `JpaRepository`
- Transactions: `@Transactional`
- Testing: `@WebMvcTest`, `@SpringBootTest`
- Profiles: `application-dev.yml`, `SPRING_PROFILES_ACTIVE`