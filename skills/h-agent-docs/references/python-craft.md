# Python Craft — Patterns and Conventions

> agent-docs 生成 Python 项目 AGENTS.md 时的语言规范来源。

## Toolchain

- 包管理：`uv`（sync / run / test）
- linter：`uv run ruff check`
- 类型检查 + test 按项目配置跑

## Type Safety: No Bare Dicts

数据模型（领域对象/跨边界数据）用 `@dataclass` / `NamedTuple` / `BaseModel`，不用 dict 实现数据类型。类型安全无特例。

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

`meta: dict` → `meta: Xxx | Yyy`，用 `@property` 封装窄化逻辑，返回纯类型不留 `| None`，**Union 运行时分派用显式条件守卫**（`if not isinstance: raise`；`-O` 会剥离 assert，运行时分派必须运行时有效）；架构保证不可能为 None 的 Infallible 才用 assert（见下节）：

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
    kind: UserFact | Note           # 不是 kind: dict
    meta: UserFact | Note | None    # 不是 meta: dict | None

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

调用方不感知 Union，不写 `isinstance`，不检查 `None`。

## Infallible Narrowing

> 通用原则见 engineering-philosophy.md「架构逻辑优先代替断言」，本模式是其 Python 落法。

你知道这里不可能为 None（架构无法消除、类型检查器无法判断），用 assert 封装在 property/method 中：

```python
@property
def current_user(self) -> User:
    assert self._user is not None, "only call when authed"
    return self._user

# 调用方拿到就是 User，不写 assert
user.name
```

## Contractual Failure

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

`dict.get()` 仅用于外部接口明确定义了默认值的可选字段。不用于内部数据的跨版本兼容。不做防御性向后兼容 fallback，除非任务明确要求。

## Docstrings（PEP 257）

公共 API（导出函数/类/模块）用 docstring 给调用者契约（参数/返回/异常），调用者不用读实现：

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

非公共/自解释代码不写 docstring（注释是最后手段，见哲学「注释纪律」）。

## Enum

### 成员比较

```python
class Color(Enum):
    RED = 1
    GREEN = 2

x is Color.RED          # correct（纯 Enum）
x.value == 1            # wrong
```

`str, Enum` 用 `==`（兼容裸字符串）。

### 显示标签

用模块级常量 + `@property label` 暴露中文/显示名：

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

## Encapsulate Process Noise

超过 2 个操作的表达式 / 多层推导式 / 重复 try-except / 不变量断言 → 封装命名函数或 property：

```python
# 不写在调用方脸上
active = [u for u in users if u.active and any(r.user_id == u.id for r in roles)]
names = [u.name for u in active]

# 写一个函数
def active_user_names(users: list[User], roles: list[Role]) -> list[str]:
    return [u.name for u in users if u.active and any(r.user_id == u.id for r in roles)]
```

## RAII / Context Manager

资源生命周期绑定作用域（哲学层 RAII Guard Pattern 的 Python 落法）：`with` + context manager，离开作用域自动释放，异常路径同样走 `__exit__`。

```python
class TempDir:
    def __enter__(self) -> Path:
        ...
    def __exit__(self, *exc: object) -> None:
        shutil.rmtree(self._path)  # 正常/异常都执行

with TempDir() as d:
    ...
```

- 文件/锁/连接/句柄一律 `with`，不裸 open/acquire 后忘 close/release
- 轻量场景用 `contextlib.contextmanager` 生成器写法
- `with` 块即作用域，guard 从不需要被读取

## DDD Layering

```text
src/<project>/
├── domain/       # 纯数据：enum, VO, aggregate（NO IO, NO Qt）
├── app/          # use-case 编排（NO Qt）
├── infra/        # IO / 设备 / 外部服务
├── gui/          # Qt 层
├── cli.py        # CLI 入口
└── config.py     # 配置
```

- domain/ 和 app/ 不能 import Qt 或 IO 库
- GUI 通过 service 接口与业务层通信

## Qt QSS 样式作用域纪律

> Qt Widgets 框架纪律，非 Python 语言问题，C++ Qt 项目同样适用。有非 Qt 的纯 Python 项目时拆为 qt-craft.md。

- **局部样式用限定选择器标注作用域**：属性选择器（`QWidget[role="x"]`）或 **ID/objectName 限定**（`#name`）；裸类型/标签选择器（`QWidget { }`）会级联到所有后代并遮蔽全局弹窗/组件规则（历史泄漏：QFileDialog 挂组件下背景透明）
- **弹窗/浮层独立挂载**，不继承容器的局部样式；全局弹窗背景规则集中在主题层（theme.py）一处
- 样式级联取**最近带样式表祖先**，局部规则会整体遮蔽全局（Qt 设计如此），选择器必须限定范围

## TYPE_CHECKING + 惰性导入

需要类型注解但不想运行时导入的依赖，用 `if TYPE_CHECKING` 前向引用 + 调用处惰性导入：

```python
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from heavy_dep import SomeClass

class MyClass:
    def __init__(self) -> None:
        self._handler: SomeClass | None = None

    def start(self) -> None:
        from heavy_dep import SomeClass  # 运行时按需加载
        self._handler = SomeClass()
```

## Cython

Cython 3 编译 `.py` 文件，需按项目测试确认覆盖范围。注意：`@dataclass`/`NamedTuple`/`BaseModel` 文件通常需排除编译（具体按项目 build 配置）。

## Do Not

- `__init__` + `self.x = x` where `@dataclass` works
- `hasattr` / `getattr` for type probing
- 防御性向后兼容 fallback（除非明确要求）
- `except: pass` 或裸 `print()` 处理错误
- 在 domain/app/infra 中 import Qt
- 跨边界返回裸 dict
