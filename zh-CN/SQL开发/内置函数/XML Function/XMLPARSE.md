```ebnf+diagram
xmlparse::= XMLPARSE "(" "DOCUMENT|CONTENT" xml_data [WELLFORMED] ")"
```

XMLPARSE函数用于将输入的字符串解析成一个XMLTYPE类型的数据。

* 使用DOCUMENT字段时，xml_data必须是一个完整的带有根节点的XML文档，且XML文件编码格式必须为UTF-8。
* 使用CONTENT字段时，xml_data可以不带根节点，但必须是一个合法的XML值。
* 当使用WELLFORMED字段时，则表明输入的xml_data是一个完整合法的XML数据，此时解析不会校验合法性，否则将校验xml_data的合法性。

**xml_data**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，待解析的XML字符串，遵循如下规则：

* 支持VARCHAR、CHAR、NVARCHAR、NCHAR、CLOB和NCLOB类型。
* 数据长度不得超过64MB。
* 如何输入的是NULL，则返回值也是NULL。

示例（HEAP表）

```sql
--缺少DOCUMENT或者CONTENT字段则报错
SELECT XMLParse('<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res FROM dual;

[1:17]YAS-04115 "DOCUMENT|CONTENT" expected but missing

--解析成DOCUMENT格式
SELECT XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<root><role1>Jerry</role1>Tom<role2></role2></root>

--解析成CONTENT格式
SELECT XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>

--解析成DOCUMENT格式并且不进行合法性检查
SELECT XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>' WELLFORMED).GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>
```
