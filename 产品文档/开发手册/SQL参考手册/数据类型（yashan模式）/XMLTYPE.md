YashanDB中的XMLTYPE类型是一种可变长度数据类型，用于存储XML类型数据，底层以CLOB方式进行存储，支持XMLTYPE类型进行建表、插入，更新和查询功能。

## 存储属性

| **类型** | 字节长度                      |
| -------- | ----------------------------- |
| XMLTYPE  | 行存：[1,4G*DB_BLOCK_SIZE] |

YashanDB的XMLTYPE类型存储方式与[大对象类型](./大对象型)的CLOB类型相同，存储包含行内存储和行外存储两种方式：

- 当一行的XMLTYPE列的数据小于4000字节时，XMLTYPE数据将存储在行内。
- 当超过4000字节时，XMLTYPE数据存入单独的大对象数据空间（可为其指定表空间），行内存储的则是指向XMLTYPE数据的指针。

## 定义格式

| **类型**  | 格式      | 规则                      |
|---------|---------| ------------------------ |
| XMLTYPE | XMLTYPE | 变长字符串，无需指定size|

使用规则如下：

- 仅适用于HEAP表，分布式HEAP表不支持。
- 对于XMLTYPE类型数据的查询为无格式的全部内容输出。
- 对于XMLTYPE类型数据的插入为无格式的直接插入，不对XMLTYPE数据的合法性做校验。
- 不支持四则运算与大小比较。
- XMLTYPE类型与其他数据类型的转换支持情况请查阅[数据类型转换](数据类型转换)。
- 支持exp/imp使用，使用规格与CLOB一致。
- 无法用于长度、截取相关函数传参，用于CONCAT、TRIM、LTRIM、RTIMR函数时返回VARCHAR类型，其余函数中表现形式与CLOB一致。
- 无法用于LOB高级包。

示例（HEAP表）

```sql
-- 1.在sales用户下创建含有XMLTYPE字段的tbl_xml表
CREATE TABLE tbl_xml (co1 xmltype); 
```

```sql
-- 2.给tbl_xml表增添数据
INSERT INTO tbl_xml VALUES('<employee><id>2</id><name>hahaha</name></employee>');
```

```sql
-- 3.查看tbl_xml表数据
SELECT * FROM tbl_xml;
```

```sql
-- 3.修改tbl_xml表数据
UPDATE tbl_xml SET co1='<employee><id>1</id><name>join</name></employee>';
```