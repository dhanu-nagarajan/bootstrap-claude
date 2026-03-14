# Java Project — Reference Output Example

## Language: Java 21
## Framework: Spring Boot 3
## Example Project Type: REST API service

## Naming Conventions

### Files
- PascalCase matching class name: `UserController.java`, `OrderService.java`, `AuthFilter.java`
- One public class per file (enforced by compiler)
- Test files: `UserControllerTest.java` or `UserControllerIT.java` (integration)

### Methods
- camelCase: `getUserById`, `createOrder`, `validateToken`
- Boolean methods: `is` or `has` prefix: `isValid()`, `hasPermission()`
- Factory methods: `of()`, `from()`, `create()`: `Money.of(100, "USD")`

### Classes
- PascalCase: `UserController`, `OrderService`, `PaymentGateway`
- Suffixes by role: `*Controller`, `*Service`, `*Repository`, `*Config`, `*Exception`
- DTOs: `*Request`, `*Response`, `*DTO`: `CreateUserRequest`, `UserResponse`

### Constants
- SCREAMING_SNAKE in static final: `MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE`
- Enum values: SCREAMING_SNAKE: `OrderStatus.PENDING`, `Role.ADMIN`

### Packages
- Reverse domain: `com.company.myservice`
- Layer-based: `controller`, `service`, `repository`, `model`, `config`, `exception`

## Type Discipline

- Use records for immutable data transfer objects (Java 16+)
- Use sealed interfaces for domain unions (Java 17+)
- Prefer `Optional<T>` over null returns for public APIs
- Use `@NonNull` / `@Nullable` annotations (JSR-305 or JetBrains)
- Validate at controller boundaries with Jakarta Validation (`@Valid`, `@NotBlank`)

Example:
```java
public record CreateUserRequest(
    @NotBlank String name,
    @Email String email,
    @NotNull Role role
) {}

public sealed interface PaymentResult
    permits PaymentResult.Success, PaymentResult.Declined, PaymentResult.Error {

    record Success(String transactionId, Money amount) implements PaymentResult {}
    record Declined(String reason) implements PaymentResult {}
    record Error(Exception cause) implements PaymentResult {}
}
```

## Patterns

### Error Handling
```java
// Custom exception hierarchy with Spring's @ResponseStatus
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String entity, Object id) {
        super("%s not found: %s".formatted(entity, id));
    }
}

// Global exception handler
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ProblemDetail handleNotFound(ResourceNotFoundException ex) {
        return ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
    }
}
```

### Module Organization
```
src/
├── main/
│   ├── java/com/company/myservice/
│   │   ├── MyServiceApplication.java
│   │   ├── controller/
│   │   │   ├── UserController.java
│   │   │   └── OrderController.java
│   │   ├── service/
│   │   │   ├── UserService.java
│   │   │   └── OrderService.java
│   │   ├── repository/
│   │   │   ├── UserRepository.java
│   │   │   └── OrderRepository.java
│   │   ├── model/
│   │   │   ├── User.java
│   │   │   └── Order.java
│   │   ├── dto/
│   │   │   ├── CreateUserRequest.java
│   │   │   └── UserResponse.java
│   │   ├── config/
│   │   │   └── SecurityConfig.java
│   │   └── exception/
│   │       └── ResourceNotFoundException.java
│   └── resources/
│       ├── application.yml
│       └── db/migration/
└── test/
    └── java/com/company/myservice/
        ├── controller/
        │   └── UserControllerTest.java
        └── service/
            └── UserServiceTest.java
```

## Forbidden Patterns

- `System.out.println()` in production code — use SLF4J/Logback
- `e.printStackTrace()` — use proper logging: `log.error("context", e)`
- Raw string SQL queries — use JPA/Hibernate named queries or Spring Data
- `@Autowired` on fields — use constructor injection (final fields)
- Catching `Exception` or `Throwable` broadly — catch specific exceptions
- Mutable DTOs (use records or Lombok `@Value`)
- `new Date()` — use `java.time` API (`Instant`, `LocalDateTime`)
- Business logic in controllers — controllers delegate to services
- `@Transactional` on private methods — Spring proxies can't intercept
- Hardcoded configuration values — use `@ConfigurationProperties`
- `Thread.sleep()` in production code — use scheduled tasks or async
- Magic numbers without named constants
- `null` returns from public methods — use `Optional<T>`
- Wildcard imports (`import java.util.*`) — use specific imports

## Testing Conventions

- Test framework: JUnit 5 + Mockito + AssertJ
- Integration tests: `@SpringBootTest` + Testcontainers
- Test file naming: `*Test.java` (unit), `*IT.java` (integration)
- Test command: `./mvnw test` or `./gradlew test`
- Use `@Nested` for grouping related tests
- Use `@DisplayName` for readable test names
- What to test: service logic, validation, error paths, controller contracts (MockMvc)
- What not to test: Spring wiring, getters/setters, generated code, JPA repositories (test via integration)
