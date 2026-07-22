Python驱动支持调用存储过程。

## 调用无参数存储过程

```python
# 调用无参数存储过程
cursor.execute("CALL procedure_name()")
connection.commit()
```

## 调用带参数存储过程

```python
# 调用带输入参数的存储过程（使用位置绑定）
cursor.execute("CALL procedure_with_params(:1, :2)", (1, 'test'))
connection.commit()
```

> **Note**：
>
> YashanDB Python驱动**仅支持** `:1`、`:2` 等位置绑定参数，或 `:参数名` 命名绑定参数，**不支持**`?`占位符。

## 调用带输出参数存储过程

YashanDB Python驱动通过`cursor.var()`方法创建绑定变量来处理OUT参数。

```python
# 调用带输出参数的存储过程

# 创建绑定变量用于接收OUT参数
out_param = cursor.var(str)

# 调用存储过程，OUT参数通过绑定变量传递
cursor.execute("CALL procedure_with_out(:param)", {"param": out_param})

# 获取OUT参数的值
print(out_param.getvalue())
```

> **Note**：
> 
> `cursor.var(str)`由驱动映射为`VARCHAR`（默认长度8000），功能正确。若需显式指定类型与长度，亦可写作`cursor.var(yaspy.VARCHAR, 100)`，与[游标对象](../Python参考信息/Python接口支持说明/游标对象.md)章节风格一致。

## 调用函数

```python
# 调用函数（使用位置绑定）
cursor.execute("SELECT function_name(:1, :2)", (1, 'test'))
result = cursor.fetchone()
print(result)
```

## 完整示例

```python
import yaspy

# 连接数据库
conn = yaspy.connect('sales/sales@192.168.1.2:1688')
cursor = conn.cursor()

# 创建测试表
cursor.execute('''CREATE TABLE IF NOT EXISTS test_proc_table (
    id NUMBER,
    message VARCHAR2(100)
)''')
conn.commit()
print('Table created successfully')

# 创建无参数存储过程
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc AS
    BEGIN
        INSERT INTO test_proc_table VALUES (1, 'Hello from procedure');
    END;
''')
conn.commit()
print('Procedure created successfully')

# 调用无参数存储过程
cursor.execute('CALL test_proc()')
conn.commit()
print('Procedure called successfully')

# 查询验证结果
cursor.execute('SELECT * FROM test_proc_table')
row = cursor.fetchone()
print(f'Result: {row}')
# Result: (1, 'Hello from procedure')

# 创建带参数存储过程
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc_with_params(p_id NUMBER, p_msg VARCHAR2) AS
    BEGIN
        INSERT INTO test_proc_table VALUES (p_id, p_msg);
    END;
''')
conn.commit()
print('Procedure with params created successfully')

# 调用带参数存储过程
cursor.execute('CALL test_proc_with_params(:1, :2)', (2, 'Test message'))
conn.commit()
print('Procedure with params called successfully')

# 查询验证结果
cursor.execute('SELECT * FROM test_proc_table ORDER BY id')
rows = cursor.fetchall()
for row in rows:
    print(f'Result: {row}')
# Result: (1, 'Hello from procedure')
# Result: (2, 'Test message')

# 创建函数
cursor.execute('''
    CREATE OR REPLACE FUNCTION test_func(p_id NUMBER) RETURN VARCHAR2 AS
        v_msg VARCHAR2(100);
    BEGIN
        SELECT message INTO v_msg FROM test_proc_table WHERE id = p_id;
        RETURN v_msg;
    END;
''')
conn.commit()
print('Function created successfully')

# 调用函数
cursor.execute('SELECT test_func(1) FROM DUAL')
result = cursor.fetchone()
print(f'Function result: {result}')
# Function result: ('Hello from procedure',)

# 创建带OUT参数的存储过程
cursor.execute('''
    CREATE OR REPLACE PROCEDURE test_proc_with_out(
        p_id NUMBER,
        p_msg OUT VARCHAR2
    ) AS
    BEGIN
        SELECT message INTO p_msg FROM test_proc_table WHERE id = p_id;
    END;
''')
conn.commit()
print('Procedure with OUT parameter created successfully')

# 调用带OUT参数的存储过程
out_msg = cursor.var(str)
cursor.execute(
    "CALL test_proc_with_out(:id, :msg)",
    {"id": 1, "msg": out_msg}
)
conn.commit()
print(f'OUT parameter result: {out_msg.getvalue()}')
# OUT parameter result: Hello from procedure

# 关闭游标和连接
cursor.close()
conn.close()
```

执行：

```python
Table created successfully
Procedure created successfully
Procedure called successfully
Result: (1, 'Hello from procedure')
Procedure with params created successfully
Procedure with params called successfully
Result: (1, 'Hello from procedure')
Result: (2, 'Test message')
Function created successfully
Function result: ('Hello from procedure',)
Procedure with OUT parameter created successfully
OUT parameter result: Hello from procedure
```

其中：
- Table created successfully：测试表创建成功
- Procedure created successfully：无参数存储过程创建成功
- Procedure called successfully：无参数存储过程调用成功
- Result: (1, 'Hello from procedure')：查询结果为第1条记录，消息内容为"Hello from procedure"
- Procedure with params created successfully：带参数存储过程创建成功
- Procedure with params called successfully：带参数存储过程调用成功
- Function created successfully：函数创建成功
- Function result: ('Hello from procedure',)：函数返回结果
- Procedure with OUT parameter created successfully：带OUT参数存储过程创建成功
- OUT parameter result: Hello from procedure：OUT参数返回的结果
