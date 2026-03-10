YashanDB中的XMLTYPE类型是数据库内置的一种自定义类型，用于存储XML类型数据。

## 存储属性

|  **类型**| 字节长度|
| -------- | ----------------------------- |
| XMLTYPE  | 行存：[1,4G*DB_BLOCK_SIZE] |

## 定义格式

|  **类型**| 格式| 规则|
|---------|---------| ------------------------ |
| XMLTYPE | XMLTYPE | 自定义对象类型，无需指定size|

## XMLTYPE属性

|  **属性名称**| 类型| 长度|
|----------|------|-----------------------|
| XML      | CLOB | [1,4G*DB_BLOCK_SIZE]  |

使用规则如下：

- 仅适用于HEAP表。
- XMLTYPE类型可通过OBJECT方法或XMLParse函数生成，可以通过`表别名.列名.属性名`的方式进行查询。
- XMLTYPE支持GetClobVal成员函数，该函数用于获取XMLTYPE的成员，返回CLOB数据类型，可以通过`表别名.列名.GetClobVal()`的方式查询数据。

示例（HEAP表）

```sql
-- 1. 在sales用户下创建含有XMLTYPE字段的tbl_xml表
CREATE TABLE tbl_xml (col xmltype); 

-- 2. 给tbl_xml表增添数据
INSERT INTO tbl_xml VALUES(XMLType('<employee><id>2</id><name>hahaha</name></employee>'));

-- 3.1 通过表别名.列名.属性名查看tbl_xml表数据
SELECT t.col.xml FROM tbl_xml t;
-- 3.2 调用getClobVal成员函数查看tbl_xml表数据
SELECT t.col.getClobVal() FROM tbl_xml t;

-- 4. 修改tbl_xml表数据
UPDATE tbl_xml SET col=XMLType('<employee><id>1</id><name>join</name></employee>');
```
