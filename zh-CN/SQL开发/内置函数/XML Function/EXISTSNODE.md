```ebnf+diagram
existsnode::= EXISTSNODE "(" xml_data "," xpath ["," namespace ]")"
```

EXISTSNODE函数用于判断xml_data中是否存在xpath路径的节点，返回1表示存在，返回0表示不存在。



**xml_data**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，遵循如下规则：

- 必须是一个完整的带有根节点的XML文档，数据类型为XMLTYPE。

- 数据长度不得超过64MB。

- 当xml_data的值为NULL时，函数返回NULL。




**xpath**

XML路径表达式，应遵循W3C（World Wide Web Consortium） XPath 1.0规范。

- 类型必须为字符型，其他类型则报错。

- xpath的最大Size为65534字节。

- 当xpath的值为NULL，函数将报错。




**namespace**

用于指定命名空间，可以有多个，不同命名空间之间可以使用逗号、空格、制表符或换行符进行分割。命名空间格式为`xmlns:prefix=href`，区分大小写，可缺省prefix，此时为默认命名空间。

- 遵循W3C(World Wide Web Consortium)的XML命名空间规范。

- 类型必须为字符型，可以缺省，缺省时默认为NULL。

- namespace的最大Size为65534字节。

- 若XML数据中存在命名空间无前缀的情况，YashanDB会自动为其添加缺省前缀`syspfx`，在指定xpath时需显式指定缺省前缀`syspfx`。



示例（HEAP表）

```sql
SELECT existsnode(XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>'), '/root') res FROM dual;

RES
----------------------------------------------------------------
1
```
