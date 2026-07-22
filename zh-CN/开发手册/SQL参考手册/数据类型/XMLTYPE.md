YashanDB中的XMLTYPE类型是数据库内置的一种自定义类型，用于存储XML类型数据。

## 存储属性

|  类型| 字节长度|
| -------- | ----------------------------- |
| XMLTYPE  | 行存：[1,4G*DB_BLOCK_SIZE] |

定义格式：

|  类型| 格式| 规则|
|---------|---------| ------------------------ |
| XMLTYPE | XMLTYPE | 自定义对象类型，无需指定size|

## XMLTYPE属性

|  属性名称| 类型| 长度|
|----------|------|-----------------------|
| XML      | CLOB | [1,4G*DB_BLOCK_SIZE]  |

## 使用规则

### 使用限制

对于XMLTYPE类型数据的使用限制如下：

- 仅适用于HEAP表。

- 不能作为GROUP BY的分组键。

- 对于[XML数据处理函数](../内置函数/XML Function/00XML Function.md)、方法以及高级包等，若无特殊说明，默认不支持访问外部资源。

### 使用方式

XMLTYPE类型数据可通过OBJECT方法或[XMLPARSE函数](../内置函数/XML Function/XMLPARSE.md)生成，可以通过`表别名.列名.属性名`的方式进行查询。

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

## 构造函数

构造函数是创建XMLTYPE对象的方法，支持从字符串、CLOB生成XML数据，并将其封装为XMLTYPE对象。

```c
constructor function XMLType( 
    xmlData IN varchar, 
    schema IN varchar default NULL, 
    validated IN number default 0, 
    wellformed IN number default 0) 
 return self as result deterministic;
```

```c
constructor function XMLType( 
    xmlData IN clob, 
    schema IN varchar default NULL, 
    validated IN number default 0, 
    wellformed IN number default 0) 
 return self as result deterministic; 
```

| 参数| 数据类型| 说明|
|--------------------|-----------|---------------------------|
| xmlData | VARCHAR或CLOB | 指定需要转换的XML数据，即原数据，数据长度不得超过64MB。 |
| schema | VARCHAR | 指定对原数据的验证规则（XML Schema），可省略。<br/>该参数仅用于兼容，无实际含义，但会进行参数值有效性校验。<br/>参数值固定为NULL，设置为其他值时函数报错。 |
| validated | NUMBER | 是否对输入的原数据进行XML Schema结构验证，需配合schema参数使用，可省略。<br/>该参数仅用于兼容，无实际含义，但会进行参数值有效性校验。<br/>有效取值为0、NULL或大于等于1的数值，默认值为0，设置为无效值时函数将报错。 |
| wellformed | NUMBER | 用于标识输入的原数据是否符合基本的语法规范，设置该标识后数据库将不对原数据进行相应的格式检查，可省略。<br/>有效取值为0、NULL或大于等于1的数值，设置为无效值时函数将报错。<br/>* 0（默认值）：表示未标识，数据库会对原数据进行相应的格式检查。<br/>* NULL：表示未标识。<br/>* 大于等于1的值：标识，不会进行格式检查。 |


## 静态函数

静态函数（Static Methods）是直接通过XMLTYPE类型调用的函数，无需实例即可操作XML数据，调用方法为`XMLTYPE.function_name()`。

createXML()函数用于将VARCHAR或CLOB类型的XML数据转换为XMLTYPE对象。

```c
STATIC FUNCTION createXML(
    xmlData IN varchar,
    schema IN varchar default null,
    validated IN number default 0,
    wellformed number default 0)
 return SYS.XMLTYPE deterministic;
```

```c
STATIC FUNCTION createXML(
    xmlData IN clob,
    schema IN varchar default null,
    validated IN number default 0,
    wellformed number default 0)
 return SYS.XMLTYPE deterministic;
```

| 参数| 数据类型| 说明|
|--------------------|-----------|---------------------------|
| xmlData | VARCHAR或CLOB | 指定需要转换的XML数据，即原数据，数据长度不得超过64MB。 |
| schema | VARCHAR | 指定对原数据的验证规则（XML Schema），可省略。<br/>该参数仅用于兼容，无实际含义，但会进行参数值有效性校验。<br/>参数值固定为NULL，设置为其他值时函数报错。 |
| validated | NUMBER | 是否对输入的原数据进行XML Schema结构验证，需配合schema参数使用，可省略。<br/>该参数仅用于兼容，无实际含义，但会进行参数值有效性校验。<br/>有效取值为0、NULL或大于等于1的数值，默认值为0，设置为无效值时函数返回NULL。 |
| wellformed | NUMBER | 用于标识输入的原数据是否符合基本的语法规范，设置该标识后数据库将不对原数据进行相应的格式检查，可省略。<br/>有效取值为0、NULL或大于等于1的数值，设置为无效值时函数返回NULL。<br/>* 0（默认值）：表示未标识，数据库会对原数据进行相应的格式检查。<br/>* NULL：表示未标识。<br/>* 大于等于1的值：标识，不会进行格式检查。 |

## 成员方法

成员方法用于查询、修改和提取XMLTYPE类型的数据内容，调用方法为`xml.function_name()`，例如可以使用`表别名.列名.GetClobVal()`查询XMLTYPE列的数据。

### existsNode()

```c
MEMBER FUNCTION existsNode(
    xpath IN varchar,
    nsmap IN varchar default null)
return number deterministic;
```

existsNode()函数用于检查XML数据中是否存在指定的节点，返回值类型为整型，返回1表示存在，返回0表示不存在。`xml.existsNode()`等价于[EXISTSNODE函数](../内置函数/XML Function/EXISTSNODE.md)。



| 参数| 数据类型| 说明|
|--------------------|-----------|---------------------------|
| xpath | VARCHAR | 指定需要搜索的XML路径，Size取值范围为[1,65534]字节，不能为NULL。 |
| nsmap | VARCHAR | 指定XML命名空间，可省略。 |



### extract()

```c
MEMBER FUNCTION extract(
    xpath IN varchar,
    nsmap IN varchar default null)
return XMLTYPE deterministic;
```

extract()函数根据XML路径抽取一个XML片段，返回XML路径匹配到的所有节点的拼接信息，返回值类型为XMLTYPE类型。`xml.extract()`等价于[XMLEXTRACT函数](../内置函数/XML Function/XMLEXTRACT.md)。



| 参数| 数据类型| 说明|
|--------------------|-----------|---------------------------|
| xpath | VARCHAR | 指定需要搜索的XML路径，Size取值范围为[1,65534]字节，不能为NULL。 |
| nsmap | VARCHAR | 指定XML命名空间，可省略。 |



### getClobVal()

```c
MEMBER FUNCTION getClobVal()
return clob deterministic;
```

getClobVal()函数用于获取XMLTYPE的成员，返回CLOB数据类型。

本函数无入参。

### getStringVal()

```c
MEMBER FUNCTION getStringVal()
return varchar deterministic;
```

getStringVal()函数用于以字符串形式返回XMLTYPE对象的值，返回一个包含序列化XML表示的字符串，或返回文本节点的文本本身。

本函数无入参。

### transform()

```c
MEMBER FUNCTION transform(
xsl IN SYS.XMLTYPE,
parammap IN varchar default NULL)
return SYS.XMLTYPE deterministic;
```

transform()函数用于根据XSL样式表和顶层参数键值对转换XML数据。

| 参数| 数据类型| 说明|
|--------------------|-----------|---------------------------|
| xsl | XMLTYPE | 指定描述转换格式的XSL样式表，支持xslt1.0全部语法。 |
| parammap  | VARCHAR | 指定XSL的顶层参数，采用name="value"键值对的样式输入。 |
