```ebnf+diagram
xmlsequence::= XMLSEQUENCE "(" xml_data ")"
```
XMLSEQUENCE函数用于将XMLType类型的XML数据中的多个节点转换为一个表，并以预置的xmltype数组格式返回。

**xml_data**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，遵循如下规则：

* 必须是一个完整的带有根节点的XML文档，数据类型为XMLTYPE。
* 数据长度不得超过64MB。
* 如果输入为NULL，则返回NULL。

示例（HEAP表）

```sql
-- 将XML数据转换成表
SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence(xmlextract(xmltype('<root><book>Java</book><book>Python</book><book>Go</book><book>Rust</book></root>'),'/root/book'))) x;

RESULT                                                           
---------------------------------------------------------------- 
<book>Java</book>                                               
<book>Python</book>                                             
<book>Go</book>                                                 
<book>Rust</book>                                               


-- 返回类型为自定义类型
SELECT TYPEOF(x.column_value) AS result FROM TABLE(XMLSequence(XMLType('<root>John Doe</root>'))) x;

RESULT                                                           
---------------------------------------------------------------- 
udt_object                                                      


-- 输入格式错误时返回报错信息
SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence('<root>John Doe</root>')) x;

[1:75]YAS-04121 invalid datatype

-- 输入NULL时返回NULL
SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence(null)) x;

RESULT                                                           
---------------------------------------------------------------- 


SQL>

```
