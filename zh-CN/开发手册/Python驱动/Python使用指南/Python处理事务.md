Python驱动支持事务的提交、回滚和隔离级别操作。

## 手动提交模式

默认情况下，YashanDB Python驱动处于手动提交模式。在此模式下，每个SQL语句执行后需要手动调用`commit()`进行提交，或者在发生错误时调用`rollback()`回滚事务。

```python
# 查看提交模式
print(connection.autocommit)  # 输出：False
```

### 适用场景

手动提交适用于需要执行多条SQL语句且这些语句需要作为一个整体事务处理的场景：

- 批量数据操作：当需要插入、更新或删除多条相关数据时，使用手动提交可以确保这些操作的原子性。

- 多表关联操作：当一个业务逻辑涉及多个表的修改时，手动提交可以保证数据一致性。

- 需要回滚能力：如果在执行过程中出现错误，需要能够回滚已执行的操作。

```python
# 默认手动提交模式
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")
cursor.execute("INSERT INTO table2 VALUES (2, 'b')")

# 提交事务
connection.commit()
```

## 自动提交

如需开启自动提交模式，可设置`autocommit`属性为`True`。在自动提交模式下，每个SQL语句执行后会自动提交。

```python
# 开启自动提交
connection.autocommit = True

# 执行的每个SQL语句会自动提交
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")  # 自动提交
```

### 适用场景

自动提交适用于以下场景：

- 简单的单条SQL操作：每次执行单条不需要事务保护的SQL语句。

- 快速原型开发：在开发调试阶段希望快速验证SQL语句执行结果。

- 读取操作：只读查询不需要事务保护。

## 回滚

```python
try:
    cursor.execute("INSERT INTO example_table VALUES (1, 'test')")
    connection.commit()
except Exception as e:
    # 发生错误时回滚
    connection.rollback()
    print(f"Error: {e}")
```

### 适用场景

回滚操作用于撤销在当前事务中所做的所有更改，通常在以下情况下使用：

- 执行错误：当SQL语句执行过程中发生错误时，回滚可以撤销已执行的部分操作。

- 业务校验失败：当业务逻辑校验不通过时，需要回滚已执行的操作。

- 用户取消操作：当用户主动取消当前操作时，回滚可以确保数据不被污染。

### 回滚到保存点

对于复杂的事务，可以创建保存点来支持部分回滚：

```python
# 执行操作并创建保存点（无需先commit，直接在事务中创建）
cursor.execute("INSERT INTO table1 VALUES (1, 'a')")
cursor.execute("SAVEPOINT sp1")  # 创建保存点
cursor.execute("INSERT INTO table2 VALUES (2, 'b')")

# 回滚到保存点（撤销table2的插入，保留table1的插入）
cursor.execute("ROLLBACK TO SAVEPOINT sp1")

# 提交事务（只提交table1的插入）
connection.commit()
```

> **Note**：
>
> - 创建保存点前**不要**先调用 `commit()`，因为 `commit()` 会提交当前事务，导致事务结束，保存点失效。
> - `ROLLBACK TO SAVEPOINT` 会撤销保存点之后的所有操作，但保留保存点之前的操作。
> - 保存点名称在同一事务中不能重复使用，回滚后该保存点即被删除。

## 事务隔离级别

事务隔离级别定义了一个事务可能受其他并发事务影响的程度。YashanDB当前仅部分支持以下隔离级别：

- READ COMMITTED（默认）：只能读取已提交的数据，避免脏读，但可能产生不可重复读。此级别**始终可用**。

- SERIALIZABLE：最高隔离级别，完全串行化执行，避免幻读，但可能影响并发性能。**仅支持作为事务的首条语句设置**。

> **Note**：
>
> - YashanDB**不支持**READ UNCOMMITTED和REPEATABLE READ隔离级别。如尝试设置，将返回语法错误。
> - SERIALIZABLE级别必须作为事务的第一条语句执行，否则会报错`YAS-02029`。

### 设置事务隔离级别

> **Note**：
>
> YashanDB Python驱动**不支持** `connection.isolation_level` 属性。如需设置事务隔离级别，请通过SQL语句进行设置。

```python
# 设置事务隔离级别（通过SQL语句）
cursor.execute("SET TRANSACTION ISOLATION LEVEL READ COMMITTED")
# 或者在连接后立即设置
conn.cursor().execute("ALTER SESSION SET ISOLATION_LEVEL = READ COMMITTED")
```

### 隔离级别选择建议

- READ COMMITTED：适用于大多数应用场景，是性能和一致性的平衡选择。

- SERIALIZABLE：适用于对数据一致性要求极高的场景，但必须确保将其作为事务的首条语句执行。

## 完整示例

```python
import yaspy

try:
    # 连接数据库
    conn = yaspy.connect(dsn='sales/sales@192.168.1.2:1688')
    cursor = conn.cursor()

    # 创建测试表
    cursor.execute('DROP TABLE IF EXISTS test_transaction')
    cursor.execute('CREATE TABLE test_transaction (id INT, name VARCHAR(50), amount DECIMAL(10,2))')
    conn.commit()
    print('Table created')

    # 1. 手动提交模式测试（默认模式）
    cursor.execute("INSERT INTO test_transaction VALUES (1, 'Product A', 100.00)")
    cursor.execute("INSERT INTO test_transaction VALUES (2, 'Product B', 200.00)")
    conn.commit()
    print('Manual commit: Records 1 and 2 committed')

    # 验证手动提交的数据
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After manual commit: {len(rows)} records')

    # 2. 开启自动提交模式测试
    conn.autocommit = True
    cursor.execute("INSERT INTO test_transaction VALUES (3, 'Product C', 300.00)")
    print('Auto-commit: Record 3 inserted')

    cursor.execute("INSERT INTO test_transaction VALUES (4, 'Product D', 400.00)")
    print('Auto-commit: Record 4 inserted')

    # 验证自动提交的数据
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After auto-commit: {len(rows)} records')

    # 3. 恢复手动提交并测试回滚
    conn.autocommit = False
    cursor.execute("INSERT INTO test_transaction VALUES (5, 'Product E', 500.00)")
    print('Record 5 inserted (will be rolled back)')

    # 执行回滚
    conn.rollback()
    print('Rollback executed')

    # 验证回滚后的数据
    cursor.execute('SELECT * FROM test_transaction')
    rows = cursor.fetchall()
    print(f'After rollback: {len(rows)} records')

    # 4. 设置事务隔离级别（通过SQL语句）
    cursor.execute("SET TRANSACTION ISOLATION LEVEL READ COMMITTED")
    print('Isolation level set to: READ COMMITTED')

    # 清理测试表
    cursor.execute('DROP TABLE test_transaction')
    conn.commit()
    print('Test table cleaned up')

    cursor.close()
    conn.close()
    print('Connection closed')

except Exception as e:
    print(f'Error: {e}')
```

执行：

```python
Table created
Manual commit: Records 1 and 2 committed
After manual commit: 2 records
Auto-commit: Record 3 inserted
Auto-commit: Record 4 inserted
After auto-commit: 4 records
Record 5 inserted (will be rolled back)
Rollback executed
After rollback: 4 records
Isolation level set to: READ COMMITTED
Test table cleaned up
Connection closed
```

其中：
- Table created：测试表创建成功
- Manual commit: Records 1 and 2 committed：手动提交1、2条记录成功
- After manual commit: 2 records：手动提交后共2条记录
- Auto-commit: Record 3 inserted：自动提交模式下第3条记录插入成功
- Auto-commit: Record 4 inserted：自动提交模式下第4条记录插入成功
- After auto-commit: 4 records：自动提交后共4条记录
- Record 5 inserted (will be rolled back)：第5条记录已插入（将回滚）
- Rollback executed：回滚执行成功
- After rollback: 4 records：回滚后仍然是4条记录（第5条被回滚）
- Isolation level set to: READ COMMITTED：隔离级别设置为READ COMMITTED
- Test table cleaned up：测试表清理完成
- Connection closed：连接关闭
