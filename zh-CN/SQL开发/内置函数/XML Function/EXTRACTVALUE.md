```ebnf+diagram
extractvalue::= EXTRACTVALUE "(" xml_data "," xpath ["," namespace ]")"
```

EXTRACTVALUE函数根据输入的xpath定位节点并提取该节点的标量值，返回结果必须是单个文本节点、属性或元素，返回类型为VARCHAR。

如果xpath指向的节点是元素，该元素必须存在一个文本节点作为其子节点，否则函数将报错，函数将返回该文本节点值。

如果xpath指向的是具有多个子节点的节点或具有非文本子节点的节点，函数将报错。



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
SELECT extractvalue(xmltype('<root>text</root>'), 'root') res FROM dual;

RES
----------------------------------------------------------------
text
```
