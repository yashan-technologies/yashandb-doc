Python驱动在执行过程中可能抛出各种异常。

## 异常类层次结构

yaspy 驱动遵循 [DB-API 2.0 规范](https://www.python.org/dev/peps/pep-0249/)，定义了以下异常类层次结构。但请注意，**yaspy 1.2.1 当前版本所有数据库操作错误均抛出 `DatabaseError`，不会细分为 `IntegrityError`、`ProgrammingError`、`OperationalError` 等**，各子异常类暂不可直接捕获。

```python
Exception (Python内置)
├── yaspy.Error
│   ├── InterfaceError
│   └── DatabaseError           # ← yaspy 当前版本唯一抛出的数据库错误类型
│       ├── DataError
│       ├── OperationalError
│       ├── IntegrityError
│       ├── InternalError
│       ├── ProgrammingError
│       └── NotSupportedError
└── yaspy.Warning
```

## 异常类说明

| 异常类 | 说明 |
| -------------------------- | -------------------- |
| yaspy.Error | 数据库错误类基类；`InterfaceError`、`DatabaseError`及其子类均继承自此类 |
| yaspy.Warning | 警告异常，当出现非致命问题时抛出（如数据截断） |
| yaspy.InterfaceError | 连接参数错误，如DSN格式错误、缺少必需参数等。**无效的主机地址或端口连接失败通常抛出`DatabaseError`而非`InterfaceError`** |
| yaspy.DatabaseError | 数据库执行错误基类。**当前版本 yaspy 所有数据库操作错误（主键冲突、SQL语法错误、表不存在等）均抛出此类型或其子类**，不会进一步细分为下表的子类型 |
| yaspy.DataError | 数据处理错误，如数据类型转换错误、数据值超范围 |
| yaspy.OperationalError | 数据库操作错误，如连接断开、事务失败、网络超时 |
| yaspy.IntegrityError | 完整性约束错误，如主键冲突、外键约束违规 |
| yaspy.InternalError | 数据库内部错误，如服务器内部异常 |
| yaspy.ProgrammingError | 编程错误，如 SQL 语法错误、无效的游标状态、错误的参数绑定 |
| yaspy.NotSupportedError | 不支持的操作，如调用 `nextset()`、`setinputsizes()` 等未实现的方法 |

> **Note**：
> 
> 上表中`DatabaseError`的子类（`DataError`、`OperationalError`、`IntegrityError`、`ProgrammingError`等）仅用于异常类层次结构定义，**当前版本yaspy不会抛出这些具体类型**，所有数据库错误均归入`DatabaseError`。如有捕获特定错误类型的需求，建议通过`DatabaseError`的`code`、`message`等属性进行区分。

## 异常处理示例

```python
import yaspy
from yaspy import Error, DatabaseError, InterfaceError

try:
    # 连接数据库
    conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

    # 执行SQL
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM example_table")

    # 获取结果
    results = cursor.fetchall()

except InterfaceError as e:
    # 连接参数错误：如DSN格式错误、缺少必需参数等
    # 注意：无效的主机地址或端口连接失败通常抛出DatabaseError
    print(f"Connection parameter error: {e}")
    print(f"Error code: {e.code}, Message: {e.message}")
except DatabaseError as e:
    # 数据库错误：主键冲突、SQL语法错误、表不存在等
    # 当前版本yaspy所有数据库操作错误均抛出此类型
    print(f"Database error: {e}")
    print(f"Error code: {e.code}, Line: {e.line}, Column: {e.column}")
    print(f"Message: {e.message}")
except Error as e:
    # 通用错误（涵盖以上所有错误类型）
    print(f"Error: {e}")
    print(f"Error code: {e.code}, Line: {e.line}, Column: {e.column}")
    print(f"Message: {e.message}")
finally:
    # 关闭连接
    if 'cursor' in locals():
        cursor.close()
    if 'conn' in locals():
        conn.close()
```

上例中`except DatabaseError`分支可覆盖以下典型错误场景：
- 主键冲突
- SQL 语法错误
- 表或视图不存在
- 事务失败、网络超时

> **Note**：
> 
> `except ProgrammingError`或`except OperationalError`**不会命中**上述任何错误，所有数据库操作错误均被`except DatabaseError`/`except Error`捕获。如需区分错误类型，请通过`e.code`或`e.message`属性进行判断。

## 异常捕获建议

- 捕获具体异常（但注意限制）：由于当前版本`yaspy`仅抛出`DatabaseError`和`InterfaceError`，建议使用这两个异常类型进行捕获，无需尝试捕获`ProgrammingError`、`OperationalError`、`IntegrityError`等子类型。

- 资源清理：使用`finally`块确保连接和游标正确关闭，避免资源泄漏。

- 不要忽略异常：避免使用空的`except`块，务必记录或处理捕获的异常。

- 通过属性区分错误类型：如需区分主键冲突、SQL语法错误等具体场景，可通过`e.code`、`e.message`等属性判断。常见错误码对应关系请参见对应章节。
