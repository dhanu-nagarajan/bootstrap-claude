# Go Project — Reference Output Example

## Language: Go
## Framework: Gin + GORM
## Example Project Type: REST API service

## Naming Conventions

### Files
- snake_case: `user_handler.go`, `order_service.go`, `auth_middleware.go`
- Test files: `user_handler_test.go` (same package, `_test.go` suffix)
- One primary type per file when possible

### Functions/Methods
- Exported: PascalCase: `GetUserByID`, `CreateOrder`, `ValidateToken`
- Unexported: camelCase: `parseRequest`, `buildQuery`, `formatResponse`
- Constructors: `New{Type}`: `NewUserService`, `NewOrderHandler`
- Getters: no `Get` prefix — `user.Name()` not `user.GetName()`

### Types/Interfaces
- Structs: PascalCase: `User`, `OrderItem`, `AuthConfig`
- Interfaces: describe behavior, no `I` prefix: `Reader`, `UserStore`, `OrderProcessor`
- Interface naming: `-er` suffix for single-method interfaces: `Validator`, `Formatter`

### Constants
- Exported: PascalCase: `MaxRetryCount`, `DefaultTimeout`
- Unexported: camelCase: `defaultPageSize`, `maxBatchSize`
- Enum-like: type + const block:
  ```go
  type Status int
  const (
      StatusPending Status = iota
      StatusActive
      StatusClosed
  )
  ```

## Type Discipline

- Use `golangci-lint` with strict configuration
- Accept interfaces, return structs
- Use custom types for domain concepts: `type UserID string`, `type Money int64`
- Avoid `interface{}` / `any` — use generics (Go 1.18+) or specific types
- Validate at boundaries (HTTP handlers, CLI input), trust internally

Example:
```go
// CreateUser accepts an interface for testability, returns concrete type
func (s *UserService) CreateUser(ctx context.Context, req CreateUserRequest) (*User, error) {
    if err := req.Validate(); err != nil {
        return nil, fmt.Errorf("invalid request: %w", err)
    }
    user := &User{
        ID:    NewUserID(),
        Name:  req.Name,
        Email: req.Email,
    }
    if err := s.store.Save(ctx, user); err != nil {
        return nil, fmt.Errorf("saving user: %w", err)
    }
    return user, nil
}
```

## Patterns

### Error Handling
```go
// Always wrap errors with context using fmt.Errorf + %w
func (s *OrderService) PlaceOrder(ctx context.Context, req PlaceOrderRequest) (*Order, error) {
    user, err := s.users.Get(ctx, req.UserID)
    if err != nil {
        return nil, fmt.Errorf("fetching user %s: %w", req.UserID, err)
    }

    order, err := NewOrder(user, req.Items)
    if err != nil {
        return nil, fmt.Errorf("creating order: %w", err)
    }

    if err := s.orders.Save(ctx, order); err != nil {
        return nil, fmt.Errorf("saving order: %w", err)
    }

    return order, nil
}

// Custom error types for domain errors
type NotFoundError struct {
    Entity string
    ID     string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s not found: %s", e.Entity, e.ID)
}
```

### Dependency Injection
```go
// Constructor injection via New functions
type UserHandler struct {
    users  UserService
    logger *slog.Logger
}

func NewUserHandler(users UserService, logger *slog.Logger) *UserHandler {
    return &UserHandler{users: users, logger: logger}
}
```

### Module Organization
```
myservice/
├── cmd/
│   └── server/
│       └── main.go            # Entry point, wire dependencies
├── internal/
│   ├── handler/               # HTTP handlers (Gin)
│   │   ├── user_handler.go
│   │   └── order_handler.go
│   ├── service/               # Business logic
│   │   ├── user_service.go
│   │   └── order_service.go
│   ├── store/                 # Database access (GORM)
│   │   ├── user_store.go
│   │   └── order_store.go
│   ├── model/                 # Domain types
│   │   ├── user.go
│   │   └── order.go
│   └── middleware/            # HTTP middleware
│       └── auth.go
├── pkg/                       # Public library code (if any)
├── go.mod
├── go.sum
└── Makefile
```

### Import Organization
```go
import (
    // stdlib
    "context"
    "fmt"
    "net/http"

    // external
    "github.com/gin-gonic/gin"
    "gorm.io/gorm"

    // internal
    "myservice/internal/model"
    "myservice/internal/service"
)
```

## Forbidden Patterns

- `panic()` in request handlers — return errors, don't crash the server
- `log.Fatal()` outside of `main()` — kills the process without cleanup
- Bare `error` returns without wrapping — lose call-site context
- `init()` functions with side effects — makes testing and dependency order unpredictable
- Global mutable state — use dependency injection
- `interface{}` / `any` when a specific type or generic would work
- Ignoring errors with `_` — handle or explicitly document why it's safe
- `time.Sleep()` in production code — use tickers, channels, or context deadlines
- Exported fields on types that should be immutable — use unexported fields + methods
- `sync.Mutex` in a struct without documenting what it protects
- `go func()` without error handling or recovery — leaked panics crash the process
- String concatenation for SQL queries — use parameterized queries
- `os.Exit()` in library code — only in `main()`
- Deep package nesting (>3 levels) — keep it flat

## Testing Conventions

- Test framework: `testing` (stdlib) + `testify/assert` for assertions
- Test file naming: `*_test.go` in same package
- Test command: `go test ./...`
- Table-driven tests for functions with multiple cases
- `testdata/` directory for test fixtures
- Test helpers: `t.Helper()` for shared setup functions
- What to test: business logic, error paths, edge cases, HTTP handlers with httptest
- What not to test: trivial getters, third-party library behavior, generated code
