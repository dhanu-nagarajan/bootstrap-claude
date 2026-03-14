# Showcase: FastAPI Service

This showcase demonstrates what bootstrap-claude generates for a Python FastAPI backend service.

**Hypothetical project:** An inventory management API with role-based access, background tasks, and PostgreSQL.

**Stack:** FastAPI, SQLAlchemy 2.0, Pydantic v2, Alembic, pytest, mypy

**Tier:** standard (14 files)

## Generated Files

```
.claude/
├── standards/
│   ├── forbidden.md       ← 14 Python/FastAPI anti-patterns
│   ├── architecture.md    ← Layered architecture boundaries
│   ├── language.md        ← PEP 8 + project-specific conventions
│   ├── testing.md         ← pytest patterns + fixtures
│   └── security.md        ← RBAC + input validation patterns
├── checklists/
│   ├── pre-commit.md      ← pytest, mypy, ruff commands
│   └── pr-review.md       ← Review dimensions
├── context/
│   ├── decisions.md       ← ADRs (SQLAlchemy 2.0 async, Pydantic v2, etc.)
│   └── glossary.md        ← Domain terms (SKU, Warehouse, StockLevel, etc.)
└── shield/
    ├── invariants.md      ← Top 10 rules
    ├── session-state.md   ← Session tracking
    └── recovery-protocol.md ← Recovery procedure
```

## Highlights

### forbidden.md — Python-Specific Depth

- "`# type: ignore` without code — use `# type: ignore[specific-error]`"
- "Bare `except:` — always catch specific exceptions"
- "`os.system()` or `subprocess.call()` with `shell=True` — use `subprocess.run()` with list args"
- "f-strings in SQL queries — use SQLAlchemy parameterized queries"
- "`from module import *` — use explicit imports"
- "Mutable default arguments — use `None` with `if arg is None: arg = []`"

### architecture.md — FastAPI Layer Boundaries

- "Route handlers in `app/api/v1/` delegate to `app/services/` — no SQLAlchemy in routes"
- "Pydantic schemas in `app/schemas/` — one file per domain entity"
- "Database models in `app/models/` — no Pydantic imports in models"
- "Background tasks in `app/tasks/` — use Celery, not FastAPI BackgroundTasks for long-running"
