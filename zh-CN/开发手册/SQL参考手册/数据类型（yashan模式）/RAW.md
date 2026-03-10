YashanDB中的RAW类型是一种与VARCHAR类似的可变长度数据类型，可以表述二进制数据或者字节字符串。

在定义表时，可以使用RAW类型声明一个列字段，通常被用于存储图形、声音、文档或二进制数据数组等内容，具体解释取决于使用情况。

存储属性
----

|  **类型**| 字节长度|
| --- |---------|
| RAW | 1~65534 |

定义格式：

|  **类型**| 格式| 规则|
| --- | --- | --- |
| RAW      | RAW(Size) | 变长二进制串，当插入的字符串小于Size时不会发生补0x00的行为。 |

Size：宽度，表示最大字节长度。

使用规则
-----

### 使用限制

对于RAW类型数据的使用限制如下：

- 不能作为索引列
- 存算一体分布式集群部署中无法作为分区键

### 数据类型转换

YashanDB支持RAW和字符型的数据类型转换，包括显式转换和隐式转换。

YashanDB支持RAW和BLOB/ROWID/UROWID/JSON的数据类型转换，支持RAW往CLOB的单向数据类型转换。

**显式转换**

用户可使用CAST、TO_CHAR等内置函数进行RAW和字符型之间的数据类型转换。

示例

```sql
SELECT CAST('123' AS RAW(5)) raw_res FROM dual;
RAW_RES 
------- 
0123   

SELECT TO_CHAR(CAST('123' AS RAW(5))) char_res FROM dual;
CHAR_RES 
-------- 
0123    
```

**隐式转换**

1.字符型向RAW类型隐式转换：

系统将连续输入的每个字符解析为二进制数据中四个连续位的十六进制表述，并将其连接后构建RAW类型数据。

输入的字符串中包含不属于十六进制表述范围（0-9、a-f、A-F）的字符时，系统报错。

如果输入字符串数量为奇数，系统将先补充4个都是0的连续位，然后再将字符串转换为RAW类型数据。

当插入或更新一个RAW列字段时，如果输入字符串的字节长度超过RAW列宽度的2倍，系统报错。

示例（HEAP表）

```sql
CREATE TABLE IF NOT EXISTS raw_table(c1 RAW(10));

--字符型向RAW类型隐式转换
INSERT INTO raw_table VALUES('1234ABCDEF');
INSERT INTO raw_table VALUES('1AB');

--不合法的十六进制表述导致隐式转换失败
INSERT INTO raw_table VALUES('G');
YAS-00223 invalid hex number

--插入字符串的字节长度超过RAW列宽度的2倍导致插入失败
INSERT INTO RAW_TABLE VALUES(LPAD(1,21,1));
YAS-04008 C1 size exceeding limit 10
```

2.RAW类型向字符型隐式转换：

系统将RAW类型数据作为以二进制输入的十六进制表述，转换为字符，其中每个字符是一个十六进制数字（0-9、a-F、a-F），表示四个连续的原始数据位。例如，将位为10101011的原始数据的一个字节做为十六进制值AB，并转换为字符串'AB'。

示例（HEAP表）

```sql
--对上例中的RAW类型数据执行字符串操作，系统进行隐式转换
SELECT GROUP_CONCAT(c1) FROM raw_table;
GROUP_CONCAT(C1)                                                 
---------------------------------------------------------------- 
1234ABCDEF,01AB 
```

3.RAW类型向BLOB类型隐式转换：

因为RAW类型和BLOB类型都可用于存储二进制对象，所以系统可以将RAW数据直接转换为BLOB数据。

示例（HEAP表）

```sql
--新建BLOB表
CREATE TABLE IF NOT EXISTS blob_table(c1 BLOB);

--可将raw_table里的数据直接插入到blob_table中
INSERT INTO blob_table SELECT c1 FROM raw_table;
```

4.BLOB类型向RAW类型隐式转换：

在BLOB数据字节长度不超过65534字节的情况下，BLOB数据可以被转换成RAW数据。

示例（HEAP表）

```sql
--可将blob_table里的数据直接插入到raw_table中
INSERT INTO raw_table SELECT c1 FROM blob_table;
```
