# Reference Example: Python Project Output

This shows what high-quality, project-specific output looks like for a Python project. Use as a calibration reference — your output should be THIS specific, never more generic.

## Example: standards/language.md (for a FastAPI + SQLAlchemy project)

```markdown
# Python Conventions

## Naming
- **Files/modules**: snake_case (`user_service.py`, `auth_middleware.py`)
- **Classes**: PascalCase (`UserService`, `DatabaseConfig`)
- **Functions/methods**: snake_case, verb-first (`get_user_by_id`, `validate_token`)
- **Constants**: SCREAMING_SNAKE (`MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE`)
- **Private**: single underscore prefix (`_internal_helper`), no dunder unless protocol
- **Type variables**: single capital letter or descriptive (`T`, `UserT`)
- **Test functions**: `test_` prefix with behavior description (`test_returns_404_for_missing_user`)

## Type Discipline
- Type hints required on all public function signatures
- `mypy --strict` must pass
- Use `typing.Protocol` for structural subtyping over ABC where possible
- Pydantic models at API boundaries (request/response validation)
- `Optional[X]` explicit — never rely on implicit `None` returns
- Use `TypeAlias` for complex union types

## Patterns (from codebase analysis)

### Dependency Injection
```python
# Pattern found in app/dependencies.py — FastAPI DI
async def get_db() -> AsyncGenerator[AsyncSession, None]:
    async with async_session_maker() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise

async def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: AsyncSession = Depends(get_db),
) -> User:
    return await authenticate_user(db, token)
```

### Repository Pattern
```python
# Pattern found in app/repositories/base.py
class BaseRepository(Generic[ModelT]):
    def __init__(self, session: AsyncSession, model: type[ModelT]):
        self.session = session
        self.model = model

    async def get_by_id(self, id: UUID) -> ModelT | None:
        return await self.session.get(self.model, id)

    async def create(self, **kwargs: Any) -> ModelT:
        instance = self.model(**kwargs)
        self.session.add(instance)
        await self.session.flush()
        return instance
```

### Error Handling
```python
# Pattern found in app/exceptions.py
class AppError(Exception):
    def __init__(
        self,
        message: str,
        code: str,
        status_code: int = 500,
        context: dict[str, Any] | None = None,
    ):
        super().__init__(message)
        self.code = code
        self.status_code = status_code
        self.context = context or {}

# Pattern found in app/middleware/error_handler.py
@app.exception_handler(AppError)
async def app_error_handler(request: Request, exc: AppError) -> JSONResponse:
    logger.error(f"{exc.code}: {exc}", extra=exc.context)
    return JSONResponse(
        status_code=exc.status_code,
        content={"error": {"code": exc.code, "message": str(exc)}},
    )
```

### Import Organization
```python
# 1. Standard library
import os
from datetime import datetime, timezone
from uuid import UUID

# 2. Third-party packages
from fastapi import APIRouter, Depends, HTTPException
from pydantic import BaseModel, Field
from sqlalchemy.ext.asyncio import AsyncSession

# 3. Local application
from app.dependencies import get_db, get_current_user
from app.models import User
from app.schemas.user import UserCreate, UserResponse
```
```

## Example: standards/forbidden.md (for a Python project)

```markdown
# Forbidden Patterns

These trigger immediate rejection in code review. No exceptions without ADR.

- `# type: ignore` without explanation — Fix the type error; if unfixable, add a comment explaining why
- Bare `except:` or `except Exception:` that swallows silently — Always log or re-raise
- `os.system()` or `subprocess.call(shell=True)` — Command injection; use `subprocess.run()` with list args
- f-strings in SQL queries — SQL injection; use parameterized queries via SQLAlchemy
- `pickle.loads()` on untrusted data — Deserialization attack; use JSON
- Mutable default arguments (`def f(items=[])`) — Shared state bug; use `None` + conditional
- `from module import *` — Namespace pollution; import explicitly
- `global` keyword — Module-level mutable state; use dependency injection
- `time.sleep()` in async code — Blocks the event loop; use `asyncio.sleep()`
- Hardcoded file paths — Use `pathlib.Path` and config
- `print()` in production code — Use structured logging via `app.core.logger`
- Nested `try/except` more than 2 levels deep — Refactor into separate functions
- `assert` for input validation — Disabled with `python -O`; use explicit checks
- Raw `dict` for API responses — Use Pydantic response models
```

## Example: checklists/pre-commit.md (for a Python/FastAPI project)

```markdown
## Pre-Commit Checklist

### Automated (must all pass)
- [ ] `make typecheck` — mypy strict mode
- [ ] `make lint` — ruff check + ruff format --check
- [ ] `make test` — pytest with coverage
- [ ] `make migrate-check` — Alembic migration head check

### Manual Verification
- [ ] No `print()`, `breakpoint()`, or `pdb` in diff
- [ ] No `.env` values, API keys, or tokens in diff
- [ ] No unrelated changes bundled in this commit
- [ ] New endpoints have Pydantic request/response models
- [ ] New database models have Alembic migrations
- [ ] New dependencies added to pyproject.toml (not pip installed directly)
- [ ] Commit message: imperative mood, explains WHY
```
