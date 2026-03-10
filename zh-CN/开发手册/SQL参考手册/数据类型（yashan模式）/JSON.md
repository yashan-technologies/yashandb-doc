YashanDB中的JSON类型是一种可变长度数据类型，通过解析符合标准JSON格式的字符串获得的二进制数据。

> **Note**: 
>
> JSON对象数据类型是基于UTF8字符集的。将字符串编码成JSON对象时，如果字符串的字符集不是UTF8，则先将字符串自动转换为UTF8字符集再进行JSON对象编码。

关于JSON格式的详细描述具体可以参考[JSON文档](../通用SQL语法/json)。

## 存储属性

|  **类型**| 字节长度|
|--------|--------------------------------|
| JSON   | 行存：<br>1-32MB<br>列存：<br>1~32MB |

定义格式：

|  **类型**| 格式| 规则|
|--------|------| -------------------------- |
| JSON   | JSON | 变长二进制串，无需指定size |

YashanDB的JSON类型存储方式与[大对象类型](./大对象型)的BLOB类型相同，存储包含行内存储和行外存储两种方式：

- 当一行的JSON列的数据小于一定的字节限制时，JSON数据将存储在行内。对于HEAP表，该限制是4000字节；对于TAC/LSC表，该限制是32000字节。
- 当超过上述字节限制时，JSON数据存入单独的大对象数据空间（可为其指定表空间）。

> **Note**: 
>
> 1. 数据在存储时会产生一些内部元信息，行存表（HEAP表）字节限制包括这部分元数据所占空间，但列存表（TAC和LSC表）字节限制不包括元数据所占空间。
> 2. 以DB_BLOCK_SIZE = 8K为例，JSON类型数据在行存表中的最大字节长度为`4G*8K=32T`。

## 使用规则

JSON表示可变长度二进制数据，数据通过解析符合标准格式的字符串得到。可以解析为JSON对象的字符串长度上限为32MB。

解析成为JSON类型的数据长度与原有的字符串长度不一定相同，大部分情况下转换为JSON类型后的数据会比原字符串更大。

JSON类型支持与字符串的相互转换，但不能进行加减乘除、比较、拼接等运算。JSON的运算能力主要由具体的JSON相关的内置函数提供。

JSON类型的列字段不能作为分区键。

示例

```sql
--1.在sales用户下创建含有JSON字段的customer_intro_json表
CREATE TABLE customer_intro_json (id INT, intro JSON);

--2.通过字符串隐式转换，插入JSON数据
INSERT INTO customer_intro_json 
VALUES (1, '{"name":"Jack", "city":"Beijing","school":"TsingHua University"}');
COMMIT;

--3.通过JSON构造函数，插入JSON数据
INSERT INTO customer_intro_json
VALUES (2, JSON('{"name":"Bob", "city":"Shenzhen","school":"Shenzhen University"}'));
COMMIT;

--4.使用JSON_QUERY函数对JSON数据进行查询
SELECT JSON_QUERY(intro, '$.name' WITH WRAPPER) name FROM customer_intro_json;
NAME
----------------------------------------------------------------
["Jack"]
["Bob"]
```
