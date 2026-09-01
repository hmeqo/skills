# Python Craft — Patterns and Conventions

> The language-spec source agent-docs uses when generating AGENTS.md for Python projects.

## Toolchain

- Package management: `uv` (sync / run / test)
- linter: `uv run ruff check`
- Type checking + tests run per project configuration

## Type Safety: No Bare Dicts

Data models (domain objects / cross-boundary data) use `@dataclass` / `NamedTuple` / `BaseModel`, never plain dicts as data types. Type safety without exceptions.

```python
@dataclass
class AppConfig:
    timeout: float = 30.0

class Point(NamedTuple):
    x: float
    y: float

class User(BaseModel):
    name: str
    age: int
```

## Union Fields + Narrowing Accessor

`meta: dict` → `meta: Xxx | Yyy`, encapsulate the narrowing logic in `@property`, return a pure type without leaking `| None`; **use explicit conditional guards for union runtime dispatch** (`if not isinstance: raise`; `-O` strips assert, so runtime dispatch must be valid at runtime); use assert only for Infallible paths the architecture guarantees can never be None (see next section):

```python
@dataclass
class UserFact:
    content: str
    importance: int

@dataclass
class Note:
    content: str
    source: str

class Memory:
    kind: UserFact | Note           # not kind: dict
    meta: UserFact | Note | None    # not meta: dict | None

    @property
    def as_user_fact(self) -> UserFact:
        if not isinstance(self.kind, UserFact):
            raise TypeError("only call on user fact")
        return self.kind

    @property
    def as_note(self) -> Note:
        if not isinstance(self.kind, Note):
            raise TypeError("only call on note")
        return self.kind

    @property
    def as_meta(self) -> UserFact | Note:
        if self.meta is None:
            raise TypeError("only call when meta present")
        return self.meta
```

Callers are unaware of the Union: no `isinstance`, no `None` checks.

## Infallible Narrowing

> General principle in engineering-philosophy.md "architecture logic over asserts"; this pattern is its Python implementation.

When you know it cannot be None here (the architecture cannot eliminate it, the type checker cannot tell), wrap the assert in a property/method:

```python
@property
def current_user(self) -> User:
    assert self._user is not None, "only call when authed"
    return self._user

# caller gets a User, no assert needed
user.name
```

## Design by Contract

```python
# WRONG — defensive fallback
val = data.get("key", default)

# CORRECT — contract violation, fail fast
val = data["key"]

# WRONG — hasattr probing
if hasattr(obj, "geoms"): ...

# CORRECT — isinstance
if isinstance(obj, MultiPolygon): ...

# WRONG — silent except
except:
    pass

# CORRECT — log with context
except Exception as exc:
    logger.warning("failed: %s", exc)
```

`dict.get()` is used only for optional fields where the external interface explicitly defines a default. Not for cross-version compatibility of internal data. No defensive backward-compatibility fallbacks, unless the task explicitly requires one.

## Docstrings (PEP 257)

Docstrings follow project convention; when used, give callers a contract (args/returns/exceptions) so they don't have to read the implementation:

```python
def fetch_rows(keys: Sequence[str]) -> dict[str, tuple[str, ...]]:
    """Fetches rows for the given keys.

    Args:
        keys: Row keys to fetch.

    Returns:
        Mapping of key to row data.

    Raises:
        KeyError: A key is missing from the table.
    """
```

Non-public / self-explanatory code gets no docstring (no explanatory comments — see philosophy "comment discipline").

## Enum

### Member comparison

```python
class Color(Enum):
    RED = 1
    GREEN = 2

x is Color.RED          # correct (pure Enum)
x.value == 1            # wrong
```

Use `==` for `str, Enum` (compatible with bare strings).

### Display labels

Expose Chinese/display names via module-level constants + `@property label`:

```python
_HATCH_LABELS = {0: "单向", 1: "双向", 2: "弓形", 3: "环形"}

class HatchType(IntEnum):
    SINGLE = 0
    BI = 1
    ARCH = 2
    RING = 3

    @property
    def label(self) -> str:
        return _HATCH_LABELS[self.value]
```

## Extract named functions

Expressions with more than 2 operations / multi-level comprehensions / repeated try-except / invariant asserts → wrap in a named function or property:

```python
# don't write this in the caller's face
active = [u for u in users if u.active and any(r.user_id == u.id for r in roles)]
names = [u.name for u in active]

# write a function
def active_user_names(users: list[User], roles: list[Role]) -> list[str]:
    return [u.name for u in users if u.active and any(r.user_id == u.id for r in roles)]
```

## RAII / Context Manager

Bind resource lifetime to scope (the Python implementation of the philosophy-level RAII Guard Pattern): `with` + context manager; released automatically on leaving scope, and exception paths go through `__exit__` too.

```python
class TempDir:
    def __enter__(self) -> Path:
        ...
    def __exit__(self, *exc: object) -> None:
        shutil.rmtree(self._path)  # runs on normal and exception paths

with TempDir() as d:
    ...
```

- Files/locks/connections/handles always use `with`; no bare open/acquire that forgets close/release
- Lightweight cases use the `contextlib.contextmanager` generator form
- The `with` block is the scope; the guard never needs to be read

## DDD Layering

```text
src/<project>/
├── domain/       # pure data: enum, VO, aggregate (NO IO, NO Qt)
├── app/          # use-case orchestration (NO Qt)
├── infra/        # IO / devices / external services
├── gui/          # Qt layer
├── cli.py        # CLI entry
└── config.py     # configuration
```

- domain/ and app/ must not import Qt or IO libraries
- GUI communicates with the business layer through service interfaces

## Qt QSS Style Scoping Discipline

> Discipline for the Qt Widgets framework, not a Python language issue; applies to C++ Qt projects too. When there are pure-Python non-Qt projects, split this into qt-craft.md.

- **Scope local styles with qualified selectors**: attribute selectors (`QWidget[role="x"]`) or **ID/objectName qualification** (`#name`); bare type/tag selectors (`QWidget { }`) cascade to all descendants and shadow global popup/component rules (historical leak: QFileDialog mounted under a component had a transparent background)
- **Mount popups/overlays independently**; they do not inherit the container's local styles; global popup background rules live in one place in the theme layer (theme.py)
- Style cascading takes the **nearest ancestor with a stylesheet**; a local rule shadows the whole global set (by Qt design), so selectors must be scoped

## TYPE_CHECKING + lazy imports

For dependencies you need for annotations but don't want to import at runtime, use `if TYPE_CHECKING` forward references + lazy imports at the call site:

```python
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from heavy_dep import SomeClass

class MyClass:
    def __init__(self) -> None:
        self._handler: SomeClass | None = None

    def start(self) -> None:
        from heavy_dep import SomeClass  # loaded on demand at runtime
        self._handler = SomeClass()
```