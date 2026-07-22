Python驱动支持处理BLOB和CLOB类型的大对象数据。

## 大对象简介

- **BLOB**：用于存储二进制数据，如图片、音频、视频、文件等非结构化数据。

- **CLOB**：用于存储大量文本数据，如长文章、XML文档、JSON数据等字符数据。

## 完整示例

```python
import yaspy

# 连接数据库
conn = yaspy.connect(dsn='sales/sales@192.168.1.2:1688')
cursor = conn.cursor()

# 创建包含BLOB和CLOB的表
cursor.execute('''CREATE TABLE IF NOT EXISTS test_lob (
    id NUMBER,
    blob_col BLOB,
    clob_col CLOB
)''')
conn.commit()
print('Table created successfully')

# 插入BLOB数据（使用位置绑定）
data = b'\x00\x01\x02\x03\x04\x05'
cursor.execute("INSERT INTO test_lob (id, blob_col) VALUES (:1, :2)", (1, data))
conn.commit()
print('BLOB inserted successfully')

# 读取BLOB数据（使用独立游标避免与var()操作冲突）
read_cursor = conn.cursor()
read_cursor.execute("SELECT blob_col FROM test_lob WHERE id = :1", (1,))
row = read_cursor.fetchone()
if row and row[0]:
    print(f'BLOB read successfully, size: {len(row[0])}')
read_cursor.close()

# 插入CLOB数据（使用位置绑定）
text = "This is a large text content " * 10
cursor.execute("INSERT INTO test_lob (id, clob_col) VALUES (:1, :2)", (2, text))
conn.commit()
print('CLOB inserted successfully')

# 读取CLOB数据（使用独立游标）
read_cursor = conn.cursor()
read_cursor.execute("SELECT clob_col FROM test_lob WHERE id = :1", (2,))
row = read_cursor.fetchone()
if row and row[0]:
    print(f'CLOB read successfully, length: {len(row[0])}')
read_cursor.close()

# 使用var()方式绑定BLOB/CLOB（使用独立游标）
var_cursor = conn.cursor()

# 插入BLOB数据（使用var）
blob_var = var_cursor.var(yaspy.BLOB)
blob_var.setvalue(b'\x00\x01\x02\x03\x04\x05')
var_cursor.execute("INSERT INTO test_lob (id, blob_col) VALUES (:1, :2)", (3, blob_var))
conn.commit()
print('BLOB inserted via var() successfully')

# 插入CLOB数据（使用var）
clob_var = var_cursor.var(yaspy.CLOB)
clob_var.setvalue("This is a large text content " * 10)
var_cursor.execute("INSERT INTO test_lob (id, clob_col) VALUES (:1, :2)", (4, clob_var))
conn.commit()
print('CLOB inserted via var() successfully')

var_cursor.close()

# 关闭游标和连接
cursor.close()
conn.close()
```

> **Note**：
>
> - 读取LOB数据时建议使用独立游标，避免与同一游标上的`var().setvalue()`操作产生冲突导致`SystemError`。
> - 使用`var()`方式插入LOB数据时，建议在独立的游标上操作。

执行：

```python
Table created successfully
BLOB inserted successfully
BLOB read successfully, size: 6
CLOB inserted successfully
CLOB read successfully, length: 290
BLOB inserted via var() successfully
CLOB inserted via var() successfully
```

其中：
- Table created successfully：测试表创建成功
- BLOB inserted successfully：BLOB数据插入成功
- BLOB read successfully, size: 6：BLOB数据读取成功，大小为6字节
- CLOB inserted successfully：CLOB数据插入成功
- CLOB read successfully, length: 290：CLOB数据读取成功，长度为290字符
- BLOB inserted via var() successfully：通过var()方式插入BLOB数据成功
- CLOB inserted via var() successfully：通过var()方式插入CLOB数据成功
